## 一 基本操作
```
db                          查看当前数据库名称，默认数据库为test，没有创建新数据库，集合将存放在test数据库中
db.stats()                  查看当前数据库信息
show dbs                    列出所有在物理上存在的数据库
use databasename            切换数据库，如果不存在，则指向该数据库，但不创建，只有插入数据/创建集合时数据库才被创建
db.dropDatabase()           删除当前指向的数据库
```
## 二 集合操作
```
show collections            查看当前数据库下的集合
db.collectionname.drop()    删除对应集合
db.createCollection(name)   创建集合，可以添加第二个参数，参数为json格式，字段为对应集合的一些可选项，如：
                            { capped : true, size : 10 }，capped（默认false）是否设置上限，当capped值为true时，需要指定size，表示上限大小，当文档达到上限时，会将之前的数据覆盖，单位为字节
```
## 三 数据操作之 增删改

 
