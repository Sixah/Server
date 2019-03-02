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