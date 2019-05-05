## 一 Nginx配置文件

#### 1.1 核心配置nginx.conf简介

Nginx的核心配置文件位于`/conf/nginx.conf` ,常见设置有：

```
worker_processes  1;                        # 工作进程数，设置太多会争夺CPU，一般设为 CPU数*核心数

events {                                    # 一般配置Nginx连接特性
    worker_connections  1024;               # 1个work同时允许多少个连接
}

http {                                      # 配置虚拟主机，一般配置多个server

    include    mime.types;                  # 主配置文件nginx.conf包含了另外一个配置文件mime.types
    default_type   application/octet-stream;  
    sendfile   on;
    keepalive_timeout  65;
    server {
        listen       80;
        server_name  localhost;
        location / {
            root   html;
            index  index.html index.htm;
        }
        error_page 500 502 503 504 /50x.html
        location = /50x.html {
            root   html;
        }
    }

}
```

该段配置的常见解读是：
- 每个server对应一个域名，有多少域名，就有多少个server，比如www.test.com,news.test.com,shop.test.com,此时nginx需要配置三个server。
- 一个server下有多个location，负责匹配url字符串

#### 1.2 常见指令

- root:  表示资源根目录，可以使用Nginx大多变量（除了`$document_root`,`$realpath_root`）,该指令可以应用于http、server、location中。  
- index: 默认首页  
- error_page code 错误页面地址: 错误页面  
- allow: 语法结构为 `allow address | CIDR | all`，用于设置允许客户端访问的IP，不支持同时设置多个，如果要设置多个，需要重复使用allow指令，CIDR表示允许客户端访问的CIDR地址，如：202.80.18.23/25，all代表允许所有。
- deny：用法同allow，表示禁止特定客户端和地址访问

注意：nginx在解析时，会依次解析，直到遇到自己匹配的才会停止，如下所示，192.168.1.0/24客户端可以访问。
```
location / {
    deny 192.168.1.1;       # 禁止访问
    allow 192.168.1.0;      # 允许访问
    deny all;               # 禁止所有
}
```

#### 1.3 基于密码配置Nginx访问权限

Nginx还支持基于HTTPBasic Authentication协议的认证。该协议是一种HTTP性质的认证办法，需要识别用户名和密码，认证书黑白的客户端不拥有访问Nginx服务器的权限，该功能由ngx_http_auth_basic_module模块支持。

```
auth_basic string | off;     # string是开启认证，并配置验证时的指示信息，off 为关闭
auth_basic_user_file file;   # 用于设置包含用户名和密码信息的绝对文件路径

# 密码文件格式：
name1:password1
name2:password2:comment
name3:password

# 密码支持crypt()函数加密，Linux上使用htpasswd命令
# htpasswd -c -d /nginx/conf/pass_file username
```

## 二 虚拟主机配置

#### 2.0 虚拟主机

虚拟主机，在web服务里是一个独立的网站站点，该站点对应独立的域名或者ip或者端口，具备独立的程序和资源目录，能独立的对外提供服务供用户访问。  

- 基于域名的虚拟主机：通过域名区分虚拟主机，常用来区分公司不同的二级域名
- 基于端口的虚拟主机：通过端口区分虚拟主机，长用来做公司内部网站，后台系统
- 基于IP的虚拟主机：通过ip区分虚拟主机，基本不用，不支持ifconfig别名，配置文件可以。

#### 2.1 基于域名的虚拟主机

```
server {
    listen 80;                      # 监听的端口
    server_name www.test1.com;      # 监听的域名：当用户访问 test1.com时候该server生效
    location / {
        root    html/test1;         # 默认文件夹，也可以是相对路径(相对于/usr/local/nginx)
        index   index.html;         # 默认取哪个页面
    }
}
server {
    listen 80;                     
    server_name bbs.test1.com;         
    location / {
        root    html/bbs;             
        index   index.html;        
    }
}
```

注意：配置完毕后，修改本地host为 nginx所在服务器主机地址 test1.com 

#### 2.2 基于端口的虚拟主机

```
server {
    listen 3000;                    # 访问www.test1.com:3000时生效
    server_name www.test1.com;      
    location / {
        root    html/test1;           
        index   index.html;      
    }
}
```

#### 2.3 基于IP的虚拟主机

```
server {
    listen 3000;                 
    server_name 192.168.1.200;      
    location / {
        root    html/test1;           
        index   html/index.html;      
    }
}
```

#### 2.4 虚拟主机别名
```
server {
    listen 80;                     
    server_name www.test1.com test1.com;        # 此时访问带或者不带www都可以进入该目录  
    location / {
        root    html/test;             
        index   index.html;        
    }
}
```

#### 2.5 虚拟主机写法优化

通常一个网站拥有多个二级域名，且拥有多个独立的后台，那么就需要配置多个虚拟主机（server），此时会造成nginx.conf文件越来越大，可以将不同的server进行拆分：
```
http {                                      
    include    mime.types;                 
    default_type   application/octet-stream;  
    sendfile   on;
    keepalive_timeout  65;
    include    extra/www.conf;
    include    extra/bbs.conf;
    include    extra/pms.conf;
}
```

## 三 虚拟主机中的location

#### 3.1 location简介

location会根据uri进行不同的定位。语法可以分为三类：
- location = patt {}    # 精准匹配，匹配成功立则停止搜索，立即处理请求
- location patt {}      # 一般匹配
- location  ~ patt {}   # 正则匹配
- location  ^~ patt {}   # 正则匹配，匹配成功后立即处理请求，而不再使用location块中的正则匹配，比如转码的地址/html/%20/data，在遇到配置`^~/html//data`时匹配成功

匹配顺序：首先看有没有精准匹配，如果有，则立即解析，停止继续查找，没有则依次按照一般匹配，正则匹配类推。  

```
# 精准匹配
location = / {
    root    html        
    index   index1.html
}

# 一般匹配
location /index.html {
    root    html
    index   index2.html
}

# 正则匹配
location ~ image {
    root    /image
    index   index3.html
}
```

#### 3.2 rewrite 重写

重写常用命令：
```
if (条件) {}    # 注意空格不能少
set             # 设置变量
return          # 返回状态码
break           # 跳出rewrite
rewrite         # 正式重写
alias           # 重新指定目录
```

条件写法：
- = 判断相等，用于字符串比较
- ~ 用来正则匹配，不区分大小写
- ~*用来正则匹配，区分大小写
- -f -d -e 依次判断是否为文件、是否为目录、是否存在

```
# 案例一：禁止某个ip访问
location / {
    if ($remote_addr = 192.168.1.100) {
        return 403
    }
    root html;
    index index.html;
}

# 案例二：正则匹配-不允许IE访问
location / {
    if ($http_user_agent ~ MSIE) {
        rewrite ^.*$ 503.html;
        break;                          # 没有break会循环重定向
    }
    root html;
    index index.html;
}
```

## 四 gzip压缩

常用参数：
```
gzip on|off                 # 是否开启
gzip_buffers 324K|168K      # 缓冲，压缩在内存中缓冲几块，每块多大
gzip_comp_level [1-9]       # 压缩级别（越高，压缩的越小，也越浪费CPU资源），推荐6
gzip_disable                # 正则匹配不压缩uri
gzip_min_length 200         # 小于多少长度不再压缩
gzip_proxied                # 设置请求者代理服务器该如何缓存内容
```
注意：图片、mp3这样的二进制文件不必压缩，一般使用CND缓存，且压缩率很低。  

## 五 缓存expires设置

举例：我们希望网站上的图片能够在用户端缓存一段时间可以设置expires。  

expires位于location或者if段里：
```
location image {
    root html/image;
    expires 1d;                     # 格式为 30s  30m  2h  30d
}

```
对于上述配置，一旦成功缓存，在有效时间内，都不会再去请求服务器了。

也可以利用浏览器的etag和last_modified_since标签，每次请求都会到达服务器，只不过浏览器会发送上述2个标签，服务器会先检测文件有没有变化，有则处理数据，没有则浏览器调用本地缓存，通常适用于变化周期短的。
 
对于静态的且可能变化周期短的数据如html，css，js则推荐该方式。  

