## 一 Nginx介绍
## 二 Nginx安装
#### 2.1 win安装
#### 2.2 Linux安装
CentOS安装：
```
# 下载
cd /usr/local/src
wget https://nginx.org/download/nginx-1.14.2.tar.gz

# 解压
tar zxvf nginx-1.14.2.tar.gz
cd /usr/local/nginx-1.14.2

# 查看依赖
·/configure                                     # 可以添加后缀--prefix=/usr/local/nginx

# 安装依赖
yum install pcre pcre-devel zlib zlib-devel     # pcre是正则库，nginx重写url需要
yum install openssl openssl-devel               # 如果使用了https，需要该库              
# 再次查看依赖，如果完毕，则编译Nginx
make && make install

# 查看安装结果
cd /usr/local/
ls                                              # 查看是否存在nginx
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
cd /usr/local/nginx
./sbin/nginx                                    # 需要关闭一些占用了80端口的应用

# 解决端口被占用
netstat -antp                                   # 查看系统端口占用情况
kill -9 (PID)

# 查看Nginx运行情况
ps aux|grep nginx                               # 此时可以看到Nginx拥有主进程和工作进程

# 如果服务器上配置了外网访问，此时可以在外网访问Nginx默认页面了

# 关闭Nginx
kill -INT (PID)                                 # 此处PID为Nginx的master进程PID
```
Nginx的主进程（master）不负责处理网络请求，负责生成和管理多个子进程。  
子进程（work）用于处理网络请求。  
## 五 信号量
在关闭Nginx时，我们使用了-INT，还有其他的信号量：
```
TERM,INT        快速关闭
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
kill -USR1 (PID)
```
注意：每次都需要查找nginx的进程pid，其实nginx的pid存储在logs/nginx.pid中，所以上述的命令可以修改为类似如下：
```
kill -HUP `cat logs/nginx.pid`
```