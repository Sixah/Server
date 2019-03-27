## 一 MySQL简介

## 二 MySQL安装

#### 2.1 win安装

#### 2.2 mac安装

#### 2.3 centOS7 源码安装mysql5.7

1 安装依赖
```
# 注意 依赖内有git，也需要安装git，这里省略
yum install -y cmake bison bison-devel libaio-devel gcc gcc-c++  ncurses-devel
```

2 下载源码
```
# 下载mysql

# 下载方式一：前往https://dev.mysql.com/downloads/mysql/ ，选择5.7，再在底部选择Source Code，再选择Generic Linux(Architecture Independent)，最后选择Compressed TAR Archive

# 下载方式二：linux上直接下载
cd /usr/local/src
wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.25.tar.gz

# 下载boost，这个版本的MySQL要求boost的版本是1.59
cd /usr/local/src
wget --no-check-certificate http://sourceforge.net/projects/boost/files/boost/1.59.0/boost_1_59_0.tar.gz
```

3 解压并编译
```
# 解压并移动boost
cd /usr/local/src
tar -zxvf  mysql-5.7.25.tar.gz 
mv boost_1_59_0.tar.gz mysql-5.7.25         # 将boost的压缩包移动至解压后的源文件目录内

# 进入mysql创建编译配置
cd mysql-5.7.25
mkdir configure
cd configure

# 生成编译环境，出现 `-- Configuring done -- Generating done `则成功，若错误，删除CMakeCache.txt后重新生成
cmake .. -DBUILD_CONFIG=mysql_release \
-DINSTALL_LAYOUT=STANDALONE \
-DCMAKE_BUILD_TYPE=RelWithDebInfo \
-DENABLE_DTRACE=OFF \
-DWITH_EMBEDDED_SERVER=OFF \
-DWITH_INNODB_MEMCACHED=ON \
-DWITH_SSL=bundled \
-DWITH_ZLIB=system \
-DWITH_PAM=ON \
-DCMAKE_INSTALL_PREFIX=/var/mysql/ \
-DINSTALL_PLUGINDIR="/var/mysql/lib/plugin" \
-DDEFAULT_CHARSET=utf8 \
-DDEFAULT_COLLATION=utf8_general_ci \
-DWITH_EDITLINE=bundled \
-DFEATURE_SET=community \
-DCOMPILATION_COMMENT="MySQL Server (GPL)" \
-DWITH_DEBUG=OFF \
-DWITH_BOOST=..

# 开始编译，出现 Linking CXX shared module udf_example.so 则编译完成
make

# 开始安装
make install
```

4 初始化数据库
```
# 添加mysql用户
useradd -s /sbin/nologin mysql

# 新建数据库文件夹及日志文件夹，并更改用户为mysql:
mkdir /mysql_data
mkdir /var/mysql/log
chown -R mysql:mysql /mysql_data/
chown -R mysql:mysql /var/mysql/log

#建立文件
touch error.log
#赋权限子目录及文件
chmod -R 777 /var/mysql/log

```

5 修改配置文件
```
vim /etc/my.cnf
# 将[mysqld]项下的内容替换为
[mysqld]
port=3306
datadir=/mysql_data
log_error=/var/mysql/log/error.log
basedir=/var/mysql/

注意：my.cnf文件有以下配置
socket=/var/lib/mysql/mysql.sock
需要手动建立mysql.sock,并赋值读写执行权限
chmod -R 777 mysql.sock
```

6 初始化数据库
```
/var/mysql/bin/mysqld  --initialize --user=mysql
ll /mysql_data/         # 查看数据文件是否生成
ll /var/mysql/log/      # 查看日志文件是否生成
```

7 配置启动文件及环境
```
# 复制配置
cp /var/mysql/support-files/mysql.server /etc/init.d/mysqld
vim /etc/init.d/mysqld

# 找到 basedir=  datadir=  修改为：
basedir=/var/mysql/
datadir=/mysql_data
```

8 启动mysql
```
/etc/init.d/mysqld start            # 也可以使用 systemctl start mysqld 此时不会有任何提示

如果出现：Unit not found，则解决方法如下：
yum install -y mariadb-server       # 首先需要安装mariadb-server
systemctl start mariadb.service     # 启动服务
systemctl enable mariadb.service    # 添加到开机启动

mysql_sceure_installation           # 进行一些安全设置
mysql -u root -p                    # 测试

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