## 一 链表LIST简介

链表LIST可以存储多个相同、不同的字符串，可以暂时想象为JS中的数组。

常用命令：
```
LPUSH key value [value value ...]           # 将value插入链表key右端，返回链表元素个数，不存在则key则创建
LRANGE key start end                        # 获取链表start到end上的所有元素值（链表从0计数）
LINDEX key index                            # 获取链表指定位置元素值，没有则返回nil
LPOP                                        # 删除链表最左侧元素，返回被删除元素
LTRIM key start end                         # 对列表进行修剪，只保留start-end的内元素

# 同理还有R开头的命令，表示从右侧开始，比如RPUSH，RPOP

# 将元素从一个列表移动到另一个列表，或者阻塞执行命令的客户端直到有其他客户端给列表添加元素为止
BLPOP key [key...] timeout                   # 从第一个菲康列表中弹出最左端元素，或者在timeout秒内阻塞并等待可弹出元素出现，同理有BRPOP
RPOPLPUSH source-key dest-key                # 从source-key列表弹出最右端元素，然后将这个元素推入dest-key列表最左端，最后返回该元素
BRPOPLPUSH source-key dest-key timeout       # 作用同 RPOPLPUSH ，但是如果source-key为空，那么在timeout秒内阻塞并等待可弹出元素出现
```

## 二 链表LIST应用场景

列表的优点是可以包含多个字符串值，使得用户可以将数据集中在同一个地方，集合也有该特性，但是集合只能保存各不相同的元素。

阻塞弹出命令以及弹出并推入命令经常用于消息传递和任务队列。