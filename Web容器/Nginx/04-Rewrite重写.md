## 一 常见后端服务器指令

#### 1.1 upstream

设置后端服务器组的主要指令，其他指令都在该指令中进行配置，其作用类似http、server块：
```
upstream name {...}     # name是给后端服务器组起的名字
```
默认情况下，某个服务器组接收到请求以后，按照轮叫制度（Round-Robin，RR）策略顺序选择组内服务器处理请求。如果一个服务器哎处理请求过程中出现错误，请求会被顺次交给组内的下一个服务器进行处理，依次类推，直到正常返回响应，如果组内所有服务器均出错，则返回最后一个服务器处理结果。  

也可以依据组内不同的服务器能力分配权重，配置权重的变量包含在server中。  

#### 1.2 server指令

该指令用于设置组内的服务器：
```
server address [parameters];            # 服务器地址可以是IP与端口、域名、uinx:等进程通信
```
parameters可以为当前服务器配置更多属性：
- weight：默认为1，为组内服务器设置权重
- max_fails,设置一个请求失败的次数，默认为1。在一定的时间范围内，当组内某台服务器请求失败的次数超过该变量设置的值时，认为该服务器无效，如果设置为0则不使用该机制。注意：404并不是请求失败。
- fail_timeout：设置max_fails的时间段
- backup：将某台组内服务器标记为备用服务器，只有当正常的服务器处于无效或者繁忙时，该服务器才被用来处理客户端请求
- down：将组内某台服务器标记为无效，通常与ip_hash配合使用

示例：
```
upstream backend{
    server backedn1.test.com weight=5;
    server 127.0.0.1 max_fails=3 fail_timeout=30s;
    server unix:/backend3;
}
```
在该示例中，设置了一个名为backend的服务器组，组内包含三台服务器，对于127.0.0.1服务器，如果30秒内连续请求3次失败，则30s内该服务器被认定为无效，域名服务器是权重最高的。

#### 1.3 ip_hash指令

该指令用于实现会话保持功能，将某个客户端的多次请求定向到组内同一台服务器上，保证客户端与服务器之间建立稳定的会话。只有当该服务器处于无效（down）时，客户端请求才会被下一个服务器接收和处理：
```
ip_hash;
```
ip_hash能够避免我们关系服务器组内各服务器之间的会话共享问题，但是该指令不能与server中的weight一起使用，且ip_hash主要根据客户端IP分配服务器，需要Nginx服务器位于所有服务器最前端。  

示例：
```
upstream backend {
    ip_hash;
    server myback1.proxy.com;
    server myback2.proxy.com;
}
```
在该示例中，当使用同一客户端访问Nginx时，将会看到一直由myback1.proxy.com来负责响应。

#### 1.4 keepalive

该指令用于控制网络连接保持功能，通过该指令，能够保证Ngxin服务器的工作进程为服务器组打开一部分网络连接，并且将数量控制在一定范围内，语法：
```
keepalive connections;  
```
connections为Nginx的每一个工作进程允许该服务器组保持的空闲网络接连数的上限值，如果超过该值，则工作进程将采用最少使用的策略关闭网络连接，该指令从Nginx1.1.4才支持。  

注意：该指令不能限制Nginx服务器工作进程能够为服务器组开启的总网络连接数，connections变量在设置时也不要设置太大，否则会影响服务器组与新网络连接的建立。

#### 1.5 least_conn

该指令用于配置Nginx服务器使用负载均衡策略为网络连接分配服务器组内的服务器。在功能上实现了最少连接负载均衡算法，在选择组内服务器时，考虑各服务器权重的同事，每次选择的都是当前网络连接最少的服务器：
```
last_conn;
```

#### 二 Rewrite

##### 2.1 Rewrite简介

Rewrite是Web服务器必备的重要基本功能，用于实现URL重写，Nginx的该功能依赖于PCRE。

URL重写的功能：
- 改变网站结构后，客户端无须变动
- 提高安全性

地址重写与地址转发的区别：
- 重写：访问google.cn最终会变成访问goodle.com的过程就是重定向
- 转发：从一个域名到另外一个已有站点的访问过程，转发后客户端地址访问地址是不变的

#### 2.1 常见指令

if判断：
```
if ($slow) {
    # 在这里设置nginx配置
}

if ($request_method) == POST {              
    return 405;
}

# 变量与正则之间使用四种连接方式： ~（区分大小写） ~*（不区分大小写） !~ !~*  （这2个匹配失败认为条件是true）

if ($http_user_agent ~ MSIE){}              # 如果$http_user_agent中包含 MSIE则会true

if ($http_cookie ~* "id=([^;]+)(?:;|$)"){}  # 使用$1和$2截取获取到的值

 # 判断请求的文件是否存在,!-f表示是否不存在，-d和!-d用来判断目录，-e和!-e用来同时判断文件或目录，-x和!-x表示判断是否可执行
if (-f $request_filename) {}               
```

break:当满足break之前的匹配，则跳出当前域
```
location / {
    if ($slow) {
        set $id $1
        break;
        limit_rate 10k;
    }
}
```

return:用于完成对请求的处理，该指令后的配置是无效的
```
return [text]               # text为响应体内容
return code URL;            # code为状态码，0-99，URL为响应地址
return URL;
```

rewrite：重写
```
server {
    listen 80;
    server_name jump.test.com;
    rewrite ^/ http://www.test.info/            # 域名跳转
}
```
