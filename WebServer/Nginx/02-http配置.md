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
