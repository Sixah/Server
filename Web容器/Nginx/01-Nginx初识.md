## 一 Nginx介绍

#### 1.1 常见Web服务器

传统Web服务器：
- Apache服务器：使用以进程为基础的结构，开支消耗很大，在多处理器环境中性能会有所下降，所以扩容Apache站点时常见的做法是增加服务器、扩充集群而不是增加处理器。 
- Tomcat：专门用于Java的JSP和Servlet的轻量级服务器软件，无法满足复杂业务场景需求，但是由于自用资源帝，安装部署方便，也有一定的占有率。
- Nginx最初是为了设计成为一个HTTP服务器，用来解决C10K问题（最初的服务器是基于进程/线程模型。新到来一个TCP连接，就需要分配一个进程。假如有C10K，就需要创建1W个进程，可想而知单机是无法承受的）。  

#### 1.2 Nginx功能

Nginx现在的功能十分强大，可以作为HTTP服务器，反向代理服务器，缓存加速访问，邮件服务器，支持FastCGI、SSL、Virtual Host、URL Rewrite、Gzip等常见功能，并支持第三方模块扩展。  

## 二 Nginx安装

#### 2.1 win安装

nginx的win安装包是绿色的，解压就可以直接使用：
```
start .\nginx.exe               # 启动后查看进程管理器中是否有nginx进程，或者访问127.0.0.1
```
但是有可能遇到无法启动问题，大多是因为80端口被占用，解决办法：  
打开regedit，找到：HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services\HTTP，找到REG_DWORD类型的项Start，将其改为0，重启电脑即可。
常用命令：
```
nginx -s stop                   //停止nginx
nginx -s reload                //重新加载nginx
nginx -s quit                  //退出nginx
```
#### 2.2 Linux源码安装

CentOS安装：
```
# 安装依赖
yum install pcre pcre-devel zlib zlib-devel     # pcre是正则库，nginx重写url需要
yum install openssl openssl-devel               # 如果使用了https，需要该库   

# 下载
wget https://nginx.org/download/nginx-1.14.2.tar.gz

# 解压
tar zxvf nginx-1.14.2.tar.gz
cd /usr/local/nginx-1.14.2

# 查看依赖，是否仍然有未安装的依赖
./configure                                     # 此处可以选择添加安装可选项               
           
# 再次查看依赖，如果完毕，则编译Nginx
make && make install                                                      
```
常用的./configure时可选参数：
| 配置可选项 | 可选项说明 |
| ------ | ------ |
| --prefix=`<path>` | nginx安装根路径，默认位于/usr/local |
| --sbin- path=`<path>` | 指定nginx二进制文件路径，没有指定则依赖于prefix |
| --conf- path=`<path>` | 指定配置文件位置 |
| --error-log -path=`<path>` | 指定nginx错误文件写入目录 |
| --pid- path=`<path>` | 指定的文件将会写入nginxmaster进程的pid，通常位于/var/run下 |
| --lock- path=`<path>` | 共享存储器互斥锁文件的路径 |
| --user=`<user>` | worker进程运行的用户 |
| --group=`<group>` | worker进程运行的组 |
| --with-file-aio | 为FreeBSD4.3+和Linux2.6.22+系统启动异步I/O |
| --with-debug | 启用调试日志，生产环境不推荐 |

#### 2.3 配置邮件代理安装

推荐的邮件代理配置为：
```
./configure --with-mail --with-mail_ssl_module --with-openssl=${BUILD_DIR}/openssl-1.0.1p
```
常见邮件代理安装配置
| 配置可选项 | 可选项说明 |
| ------ | ------ |
| --with-mail | 启用mail模块，该模块默认没有被激活 |
| --with-mail_ssl_module | 启用ssl |
| --without-mail_pop3_module | 启用mail模块后，单独禁用POP3模块 | 
| --without-mail_imap_module | 启用mail模块后，单独禁用IMAP模块 |
| --without-mail_smtp_module | 启用mail模块后，单独禁用SMTP模块 |
| --without-http | 禁用http模块，只使用mail | 

## 三 Nginx文件目录

在Nginx安装目录中，存在四个目录：
```
conf    配置文件目录
html    静态文件目录
logs    日志文件目录
sbin    命令目录
```

## 四 Nginx启动与关闭

Nginx服务在运行时，会保持一个主进程和一个或多个工作进程，通过给Nginx服务的主进程发送信号就可以控制服务的启动停止了。

```
# 启动
cd /usr/local/nginx/sbin
./nginx                                         # 需要关闭一些占用了80端口的应用

# 查看Nginx运行情况，如果服务器上配置了外网访问，此时可以在外网访问Nginx默认页面了
ps aux|grep nginx                               # 此时可以看到Nginx拥有主进程和工作进程
cat /usr/local/nginx/logs/nginx.pid             # 该方法也可以查看nginx主进程

注意：Nginx的主进程（master）不负责处理网络请求，负责生成和管理多个子进程，子进程（work）用于处理网络请求。

# 如果出现80端口被占用的情况，解决办法
netstat -antp                                   # 查看系统端口占用情况
kill -9 (PID)

# 关闭Nginx
kill -INT PID                                   # 此处PID为Nginx的master进程PID
```

## 五 信号量

在关闭Nginx时，我们使用了-INT，还有其他的信号量：
```
TERM或者INT     快速关闭
QUIT            优雅的关闭进程（等请求结束后再关闭）
HUP             重要！！！改变配置文件时用来，平滑的重读配置文件
USR1            重读日志，在日志按月/日分割时使用
USR2            平滑升级Nginx版本
WINGCH          优雅关闭旧进程，配合USR2进行升级
```
由于Linux文件系统的特性，在Nginx的日志目录中，我们经常期望日志文件以日期命名，直接修改文件名，日志还是会往改名后的文件不停的录入。  
正确的操作步骤如下：
```
# 修改日志文件文件名等

# 重读日志
kill -USR1 PID
```
注意：每次都需要查找nginx的进程pid，其实nginx的pid存储在logs/nginx.pid中，所以上述的命令可以修改为类似如下：
```
kill -HUP `cat logs/nginx.pid`
```
注意：Nginx的命令虽然是以kill配合信号量来做，但是我们可以通过下面的命令查看Nginx真正的命令配置：
```
./nginx -h             # 查看命令帮助
./nginx -t             # 查看配置文件书写是否正确
```