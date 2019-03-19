## 一 字符串STRING简介

Redis的字符串是由字节组成的序列，可以存储：字节串（byte string），整数取值范围取决于操作系统位数，整数也能转换为浮点数，浮点数取值范围和精度则与IEEE754标准的 双精度浮点数一致。

常用命令：
```
SET key value                   # 设置key的值为value，成功则返回OK，key存在则替换原key值为value，key不存在则创建并设置
GET key                         # 获取key的值，成功则返回键值，不存在该key返回nil，
DEL key                         # 删除key，返回被删除的key数量

# 自增自减命令:如果不存在则会创建一个值为0的key，并同时进行自增自减。 
# 如果对value为空的键执行自增自减，value被redis默认为0，如果value是无法解析为十进制数，则返回错误
INCR key                        # key的值自增1
INCRBY key amount               # key值增加amount
INCRBYFLOAT key amount          # key值加上浮点数amount(redis2.6以上支持)
DECR key                        # key值自减1
DECRBY key amount               # key值减少amount

# 常见处理子串命令
APPEND key value                # 将value追加到key值末尾
GETRANGE start end              # 获取范围内字符串
SETRANGE offset value           # 将偏移量offset开始的子串设置为value
```

## 二 字符串STRING数据结构