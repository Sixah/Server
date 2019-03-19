## 一 有序集合ZSET简介

有序集合ZSET与散列一样，存储着键值对，有序集合的键称为成员，每个成员是各不相同的，而集合的值被称为分值（score，浮点数）。  

有序集合与散列：都可以根据成员访问元素，但是有序集合还可以根据分值、分值排序访问元素的结构。  

ZSET命令：
```
# 常用命令
ZADD key score member [score memeber...]            # 将一个带有给定分值的成员添加到有序集合里
ZREM key member [member...]                         # 从集合中移除指定成员，返回被移除成员数量
ZCARD key                                           # 返回集合中成员数量
ZINCRBY key increment member                        # 将member分值增加increment
ZCOUNT key min max                                  # 返回介于min和max之间的成员数量
ZRANK key member                                    # 返回成员memeber在有序集合中的排名
ZSCORE key memeber                                  # 返回成员的member分值
ZRANGE key start end [WITHSCORES]                   # 返回有序集合中排名介于start和end之间的成员，如果给定了可选项，那么命令会将成员的分值也一并返回

# 范围类型数据操作


# 集合间操作命令

```