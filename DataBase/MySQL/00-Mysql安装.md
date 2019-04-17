## win安装

## mac安装

## Linux安装mysql方式

安装方式：
- 源码安装： 高级运维安装方式，需要编译，且可以自定义配置和模块
- 二进制安装：较为高效，安装文件体积较大，因为是已经编译好的文件，安装后会提供默认的mysql目录
- yum安装：更简单的二进制安装方式，适合对数据库要求不太高的场合，例如并发不大，公司内部，企业内部的一些应用场景

安装原则：  
源码安装虽然提供了高度定制性，但是当前的互联网企业都是集群架构，上百集群中使用源码方式安装mysql显然压力很大，推荐使用二进制方式安装

####  CentOS7 二进制方式安装 mysql5.7

0 准备工作
```
# 关闭防火墙
systemctl stop firewalld.service        # 停止firewall
systemctl disable firewalld.service     # 禁止firewall开机启动
firewall-cmd --state                    # 查看默认防火墙状态,notrunning为关闭

# 删除mysql，mariadb
rpm -qa|grep mariadb
yum remove mariadb*
rpm -qa|grep mysql
yum remove mysql*
rm /etc/my.cnf   
rm /etc/init.d/mysqld                              
```

1 下载并上传安装包
```
选择：MySQL Community Server 5.7 -> Linux-Generic 64 -> Compressed TAR Archive
或者：wget https://cdn.mysql.com//Downloads/MySQL-5.7/mysql-5.7.25-linux-glibc2.12-x86_64.tar.gz
```

2 解压
```
tar zxvf mysql-5.7.25-linux-glibc2.12-x86_64.tar.gz
mv mysql-5.7.25-linux-glibc2.12-x86_64 /usr/local/mysql
```

3 创建用户
```
groupadd mysql
useradd -r -g mysql -s /bin/false mysql         # 创建一个不可登陆的mysql用户
```

4 创建数据目录
```
cd /usr/local/mysql
mkdir data
chown -R mysql.mysql .
```

5 初始化数据库，此时会生成临时密码，需要记录下来
```
/usr/local/mysql/bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data
```
注意：此时会生成一个临时密码，需要记录

6 建立配置
```
vim /etc/my.cnf
# 添加内容
[mysqld]
basedir=/usr/local/mysql
datadir=/usr/local/mysql/data
```

7 启动mysql
```
# 启动方式一（不推荐） 手动启动
/usr/local/mysql/bin/mysqld_safe --user=mysql &

# 启动方式二（推荐） 并设置为开机启动
cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysqld
chmod a+x /etc/init.d/mysqld                        
chkconfig --add /etc/init.d/mysqld
chkconfig mysqld on
service mysqld start                                # 如果之前启动过，使用 restart
```

8 本地连接
```
/usr/local/mysql/bin/mysql -uroot -p                # 输入刚才保存的密码
```

9 重置密码
```
# 本地连接后执行：
alter user root@'localhost' identified by '(test123)'; # 密码被修改为：(test123)
```

10 授权远程访问
```
grant all privileges on *.* to root@'%' identified by '123456';
flush privileges;
```
解释：
- `*.*`分别代表所有的数据库名和所有的数据库表
- root@’%’中的root代表用户名，%代表ip地址，%也可以指定具体的ip，如：root@localhost,root@192.168.10.129
- flush是授权刷新命令；

#### CentOS7 源码方式安装 mysql5.7

0 准备工作
```
# 升级系统
yum update

# 卸载原有的mysql和mariadb
rpm -qa|grep mariadb
yum remove mariadb*
rpm -qa|grep mysql
yum remove mysql*
rm /etc/my.cnf
rm /etc/init.d/mysqld

# 注意 依赖内有git，也需要安装git，这里省略
yum install -y cmake bison bison-devel libaio-devel gcc gcc-c++  ncurses-devel
```

1 下载源码
```
# 下载方式一：前往https://dev.mysql.com/downloads/mysql/ ，选择5.7，再在底部选择Source Code，再选择Generic Linux(Architecture Independent)，最后选择Compressed TAR Archive

# 下载方式二：linux上直接下载
wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.25.tar.gz
```

2 创建mysql数据目录
```
# 创建用户
groupadd mysql
useradd -s /bin/bash -m -g mysql mysql

# 创建mysql数据
mkdir -p /data/mysql/data               # 数据文件目录
mkdir -p /data/mysql/logs               # 日志文件目录

# 设置数据目录权限
chown -R mysql:mysql /data/mysql/
```

3 解压并编译 
```
tar zxvf mysql-5.7.25.tar.gz
cd mysql-5.7.25
wget --no-check-certificate http://sourceforge.net/projects/boost/files/boost/1.59.0/boost_1_59_0.tar.gz
mkdir configure
cd configure

# 出现 -- Configuring done -- Generating done 则成功

cmake .. -DBUILD_CONFIG=mysql_release \
-DINSTALL_LAYOUT=STANDALONE \
-DCMAKE_BUILD_TYPE=RelWithDebInfo \
-DENABLE_DTRACE=OFF \
-DWITH_EMBEDDED_SERVER=OFF \
-DWITH_INNODB_MEMCACHED=ON \
-DWITH_SSL=bundled \
-DWITH_ZLIB=system \
-DWITH_PAM=ON \
-DCMAKE_INSTALL_PREFIX=/usr/local/mysql/ \
-DINSTALL_PLUGINDIR="/usr/local/mysql/lib/plugin" \
-DDEFAULT_CHARSET=utf8 \
-DDEFAULT_COLLATION=utf8_general_ci \
-DWITH_EDITLINE=bundled \
-DFEATURE_SET=community \
-DCOMPILATION_COMMENT="MySQL Server (GPL)" \
-DWITH_DEBUG=OFF \
-DWITH_BOOST=..



# 编译安装
make 
make install

# 设置安装目录权限
chown -R mysql:mysql /usr/local/mysql
```

4 设置配置文件
```
vim /etc/my.cnf

# 添加如下配置

[mysqld]
character-set-server = utf8mb4
collation-server = utf8mb4_general_ci

skip-external-locking
skip-name-resolve

user = mysql
port = 3306

basedir = /usr/local/mysql
datadir = /data/mysql/data
log-error = /data/mysql/logs/error.log

socket = /data/mysql/mysql.sock
pid-file = /data/mysql/mysql.pid
slow-query-log-file = /data/mysql/logs/slow.log

[mysqld_safe]
log-error=/data/mysql/mysqld.log
pid-file=/data/mysql/mysqld.pid

# 设置权限
chown -R mysql:mysql /etc/my.cnf
```

5 初始化mysql
```
cd /usr/local/mysql
./bin/mysqld --defaults-file=/etc/my.cnf --initialize --basedir=/usr/local/mysql --datadir=/data/mysql/data --user=mysql

ll /data/mysql/data/        # 查看数据文件是否生成
ll /data/mysql/logs/         # 查看日志文件是否生成
```

6 配置启动文件及环境
```
# 复制配置
cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysqld
vim /etc/init.d/mysqld

# 找到 basedir=  datadir=  修改为：
basedir=/usr/local/mysql/
datadir=/data/mysql/data/
```

8 启动mysql
```
# 启动
/etc/init.d/mysqld start            # 也可以使用 systemctl start mysqld 此时不会有任何提示

 # 测试
mysql -u root -p                   
```

9 设置mysql开机自启动
```
systemctl enable mysqld
vim /root/.bash_profile
# 将 PATH=$PATH:$HOME/bin  修改为：
PATH=$PATH:$HOME/bin:/var/mysql/bin
```

10 修改root密码
```
# MySQL从5.7开始不支持安装后使用空密码进行登录，因此在这里需要先查询程序生成的临时密码：
[root@localhost ~]# cat /var/mysql/log/error.log |grep 'A temporary password'

# 关闭mysql 修改配置
vim /etc/my.cnf 
skip-grant-tables=1       # 添加该句

# 重启mysql，此时无须登密码登录了，不过重启可能需要下列目录：
mkdir /var/run/mysqld
chmod 777 /var/run/mysqld/
/etc/rc.d/init.d/mysqld start
参考地址：https://blog.csdn.net/hjf161105/article/details/78850658

# 连接后修改密码：
update mysql.user set authentication_string=PASSWORD('PET1314sum'),password_expired='N',password_last_changed=now() where user='root';

https://blog.csdn.net/sssssscccccc/article/details/80919826
```