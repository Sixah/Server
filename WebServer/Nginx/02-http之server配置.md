## 一 Nginx配置文件

#### 1.1 核心配置nginx.conf

Nginx的核心配置文件位于`/conf/nginx.conf` ,常见设置有：

```
worker_processes  1;                # 多少个工作进程，设置太多会争夺CPU，一般设为 CPU数*核心数

events {                            # 一般配置Nginx连接特性
    worker_connections  1024;       # 1个work同时允许多少个连接
}

http {                              # 配置虚拟主机，一般配置多个server

     server {
        listen       80;
        server_name  localhost;

        location / {
            root   html;
            index  index.html index.htm;
        }

        location = /50x.html {
            root   html;
        }
    }

}
```

## 二 虚拟主机配置

#### 2.1 基于域名的虚拟主机

```
server {
    listen 80;                      # 监听的端口
    server_name test1.com;          # 监听的域名：当用户访问 test1.com时候该server生效

    location / {
        root    test1;              # 默认文件夹，也可以是相对路径(相对于/usr/local/nginx)
        index   index.html;         # 默认取哪个页面
    }
}
```

注意：配置完毕后，修改本地host为 nginx所在服务器主机地址 test1.com 

#### 2.2 基于端口的虚拟主机

```
server {
    listen 8000;                    # 访问test1.com:8000时生效
    server_name test1.com;      

    location / {
        root    test1;           
        index   index.html;      
    }

}
```

#### 2.3 基于IP的虚拟主机

```
server {
    listen 8000;                 
    server_name 192.168.1.200;      

    location / {
        root    test1;           
        index   index.html;      
    }
}
```


## 三 location写法

#### 3.1 location简介

location会根据uri进行不同的定位。语法可以分为三类：
- location = patt {}    # 精准匹配
- location patt {}      # 一般匹配
- location  ~ patt {}   # 正则匹配

匹配顺序：首先看有没有精准匹配，如果有，则立即解析，停止继续查找匹配，依次按照一般匹配，正则匹配类推。  

```
# 精准匹配
location = / {
    root    /var/www/html1
    index1.html
}

# 一般匹配
location /index.html {
    root    /var/www/html2
    index2.html
}

# 正则匹配
location ~ image {
    root    /var/www/image
    index3.html
}
```

## 三 rewrite 重写

重写常用命令：
```
if (条件) {}    # 注意空格不能少
set             # 设置变量
return          # 返回状态码
break           # 跳出rewrite
rewrite         # 正式重写
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

