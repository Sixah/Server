## 一 Redis初识
#### 1.1 Redis简介
Redis（Remote Dictionary Server）是使用C语言编写的开源高性能键值存储数据库。Redis基于内存存储，又具备持久化功能，是当前最热门的NoSQL数据库之一。

#### 1.2 Redis安装
redis解压后就可以直接使用了（不像Nginx等Linux软件需要configure，redis官方已经配置过了）。
```
# 下载并加压redis，进入redis目录
make            # 编译
make test       # 如果提示需要tcl库，需要安装tcl
make PREFIX=/usr/local/redis install    # 执行安装

cd /usr/local/redis
cp ~/src/redis-5.0/redis.conf ./   # 复制一份配置文件到redis下
```
#### 1.3 启动 
```
cd /usr/local/redis/
./bin/redis-server redis.conf 

# 以守护进程方式运行需要修改配置文件：daemonize yes
```

#### 1.4 连接

```
./redis-cli
ping                # 得到回复 PONG
```

## 二 redis通用操作

```
# 设置键
set test 123            # 设置一个值为123的键test

# 查询值
get test                # 得到"123"

# 查询键名
keys *                  # 查询当前所有的key
keys test               # 使用keys命令进行精确查找key
keys t*                 # 使用keys命令进行模糊查找key
keys t[e|s]st           # 使用keys进行匹配查找key
keys t??t               # 使用keys进行模糊匹配

# 删除key
del test                # 删除key，成功返回1，失败返回0

# 改key名
rename test test2       # 修改test名为test2，若test2原本存在，则替换
renamenx test test2     # 若test2原本存在，则返回0

# 有效期
ttl test                # 查询有效期，返回值-1为永久，key不存在也返回-2
expire test 60          # 设置键test有效期为60秒
pexpire test 1000       # 设置毫秒数
pttl test               # 查询毫秒有效期
persist test            # 设置为永久有效

# 选择数据库
select 1                # 选择数据库，redis默认有16个数据库，默认链接0号

# 其他
randomkey               # 随机获取一个key名
exists test             # 查询键是否存在，存在返回1，不存在返回0
type test               # 查询key类型
move test 1             # 将test移动到1号库
```