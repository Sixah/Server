## 一 散列HASH简介

散列可以存储多个键值对之间的映射，可以把散列想象为一个猥琐版的Redis或者类似JSON。 

常用命令：
```
# 散列键操作
HSET                                # 设置散列键值对
HGETALL                             # 获取指定散列键的值
HLEN key                            # 获取散列包含的键值对数量

# 散列的键值操作
HMGET key k [k...]                  # 获取散列中一个或者多个键k的值
HMSET key k v [k v...]              # 为散列内一个或多个键设置值
HDEL key k [k]                      # 如果给定键存在于散列里面，那么移除这个键

# 散列高级特性
HEXISTS key k                       # 检查散列key内指定的键k是否存在
HKEYS key                           # 获取散列key内所有的键
HVALS key                           # 获取散列key包含的所有值
HGETALL key                         # 获取散列key包含的所有键值对
HINCRBY key k increment             # 将k值增加整数increment
HINCRBYFLOAT key k increment        # 将k值增加浮点数increment
```

注意事项：如果散列非常大，可以先使用HKEYS获取所有的键，然后通过只获取必要的值来减少需要传输的数据量。

## 二 应用场景

散列HASH常用语存储用户信息，如uid与token。

## 三 数据结构

