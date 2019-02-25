## 一 Nginx介绍
Nginx最初是为了设计成为一个HTTP服务器，用来解决C10K问题。  
关于C10K:最初的服务器是基于进程/线程模型。新到来一个TCP连接，就需要分配一个进程。假如有C10K，就需要创建1W个进程，可想而知单机是无法承受的。
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
./configure                                    
           
# 再次查看依赖，如果完毕，则编译Nginx
make && make install                            # 默认安装在/usr/local下                            
```
## 三 Nginx文件目录
在Nginx安装目录中，存在四个目录：
```
conf    配置文件目录
html    静态文件目录
logs    日志文件目录
sbin    命令目录
```
## 四 Nginx启动与关闭
```
# 启动
cd /usr/local/nginx/sbin
./nginx                                         # 需要关闭一些占用了80端口的应用

# 查看Nginx运行情况，如果服务器上配置了外网访问，此时可以在外网访问Nginx默认页面了
ps aux|grep nginx                               # 此时可以看到Nginx拥有主进程和工作进程
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