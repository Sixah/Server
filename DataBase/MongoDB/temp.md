超级管理员
为了更安全的访问mongodb，需要访问者提供用户名和密码，于是需要在mongodb中创建用户
采用了角色-用户-数据库的安全管理方式
常用系统角色如下：
root：只在admin数据库中可用，超级账号，超级权限
Read：允许用户读取指定数据库
readWrite：允许用户读写指定数据库
创建超级管理用户
use admin
db.createUser({
    user:'admin',
    pwd:'123',
    roles:[{role:'root',db:'admin'}]
})
启用安全认证
修改配置文件
sudo vi /etc/mongod.conf
启用身份验证
注意：keys and values之间一定要加空格, 否则解析会报错
security:
  authorization: enabled
重启服务
sudo service mongod stop
sudo service mongod start
终端连接
 mongo -u 'admin' -p '123' --authenticationDatabase 'admin'
普通用户管理
使用超级管理员登录，然后进入用户管理操作
查看当前数据库的用户
use test1
show users
创建普通用户
db.createUser({
    user:'t1',
    pwd:'123',
    roles:[{role:'readWrite',db:'test1'}]
})
终端连接
mongo -u t1 -p 123 --authenticationDatabase test1
切换数据库，执行命令查看效果

修改用户：可以修改pwd、roles属性

db.updateUser('t1',{pwd:'456'})


复制（副本集）
什么是复制
复制提供了数据的冗余备份，并在多个服务器上存储数据副本，提高了数据的可用性，并可以保证数据的安全性
复制还允许从硬件故障和服务中断中恢复数据
为什么要复制
数据备份
数据灾难恢复
读写分离
高（24* 7）数据可用性
无宕机维护
副本集对应用程序是透明
复制的工作原理
复制至少需要两个节点A、B...
A是主节点，负责处理客户端请求
其余的都是从节点，负责复制主节点上的数据
节点常见的搭配方式为：一主一从、一主多从
主节点记录在其上的所有操作，从节点定期轮询主节点获取这些操作，然后对自己的数据副本执行这些操作，从而保证从节点的数据与主节点一致
主节点与从节点进行数据交互保障数据的一致性
复制的特点
N 个节点的集群
任何节点可作为主节点
所有写入操作都在主节点上
自动故障转移
自动恢复
设置复制节点
接下来的操作需要打开多个终端窗口，而且可能会连接多台ubuntu主机，会显得有些乱，建议在xshell中实现
step1:创建数据库目录t1、t2
在Desktop目录下演示，其它目录也可以，注意权限即可
mkdir t1
mkdir t2
step2:使用如下格式启动mongod，注意replSet的名称是一致的
mongod --bind_ip 192.168.196.128 --port 27017 --dbpath ~/Desktop/t1 --replSet rs0
mongod --bind_ip 192.168.196.128 --port 27018 --dbpath ~/Desktop/t2 --replSet rs0
step3:连接主服务器，此处设置192.168.196.128:27017为主服务器
mongo --host 192.168.196.128 --port 27017
step4:初始化
rs.initiate()
初始化完成后，提示符如下图：
初始化

step5:查看当前状态
rs.status()
当前状态如下图：
初始化

step6:添加复本集
rs.add('192.168.196.128:27018')
step7:复本集添加成功后，当前状态如下图：
初始化

step8:连接第二个mongo服务
mongo --host 192.168.196.128 --port 27018
连接成功后，提示符如下图：
初始化

step9:向主服务器中插入数据
use test1
for(i=0;i<10;i++){db.t1.insert({_id:i})}
db.t1.find()
step10:在从服务器中插查询
说明：如果在从服务器上进行读操作，需要设置rs.slaveOk()
rs.slaveOk()
db.t1.find()
其它说明
删除从节点
rs.remove('192.168.196.128:27018')
关闭主服务器后，再重新启动，会发现原来的从服务器变为了从服务器，新启动的服务器（原来的从服务器）变为了从服务器


备份
语法
mongodump -h dbhost -d dbname -o dbdirectory
-h：服务器地址，也可以指定端口号
-d：需要备份的数据库名称
-o：备份的数据存放位置，此目录中存放着备份出来的数据
例1
sudo mkdir test1bak
sudo mongodump -h 192.168.196.128:27017 -d test1 -o ~/Desktop/test1bak
恢复
语法
mongorestore -h dbhost -d dbname --dir dbdirectory
-h：服务器地址
-d：需要恢复的数据库实例
--dir：备份数据所在位置
例2
mongorestore -h 192.168.196.128:27017 -d test2 --dir ~/Desktop/test1bak/test1