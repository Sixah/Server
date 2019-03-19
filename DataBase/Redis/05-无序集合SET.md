## 一 集合SET简介

集合SET通过散列保证存储的元素（即每个字符串）都是各不相同的，由于采用无序方式存储，集合SET不能插入元素到两端。  

常用命令：
```
SADD key item [item...]                 # 将给定元素添加到集合，返回被添加的元素数(不包括重复的)
SREM key item [item...]                 # 移除指定元素，返回被移除的元素数量
SISMEMBER key item                      # 检查指定元素是否存在于集合
SCARDA key                              # 返回集合包含的元素数量
SMEMEBERS                               # 返回集合包含的所有元素
SRANDMEMBER key [count]                 # 从集合里随机返回一个或者多个元素，当count为正数，返回的随机元素不会重复，count为负数可能重复
SPOP key                                # 随机移除一个元素，返回被移除的元素
SMOVE key source-key dest-key item      # 如果集合source-key包含元素item，那么移除该元素并添加到dest-key中，移除成功返回1，否则返回0

# 多集合命令
SDIEF key [key...]                      # 差集：返回存在于第一个集合，而不存在于其他集合中的元素
SDIEFSTORE dest-key key [key...]        # 差集：返回存在于第一个集合，而不存在于其他集合中的元素，并存储到dest-key
SINTER key [key...]                     # 交集：返回同时存在于所有集合中的元素
SINTERSTORE dest-key key [key...]       # 交集：返回同时存在于所有集合中的元素，并存储到dest-key
SUNION key [key...]                     # 并集：返回至少存在于一个集合的元素
SUNIONSTORE dest-key key [key...]       # 并集：返回至少存在于一个集合的元素，并存储到dest-key
```