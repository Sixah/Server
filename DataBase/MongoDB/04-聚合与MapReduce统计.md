## 一 聚合
#### 1.1 聚合、管道、表达式
聚合(aggregate)主要用于计算数据，类似sql中的sum()、avg()。  
```
db.集合名称.aggregate([{管道:{表达式}}])
```
管道在Unix和Linux中一般用于将当前命令的输出结果作为下一个命令的输入，比如如下命令：
```
ps ajx | grep mongo
```
在mongodb中，管道具有同样的作用，文档处理完毕后，通过管道进行下一次处理。  
常用管道：
- $group：将集合中的文档分组，可用于统计结果
- $match：过滤数据，只输出符合条件的文档
- $project：修改输入文档的结构，如重命名、增加、删除字段、创建计算结果
- $sort：将输入文档排序后输出
- $limit：限制聚合管道返回的文档数
- $skip：跳过指定数量的文档，并返回余下的文档
- $unwind：将数组类型的字段进行拆分
常用表达式：
- $sum：计算总和， $sum:1同count表示计数
- $avg：计算平均值
- $min：获取最小值
- $max：获取最大值
- $push：在结果文档中插入值到一个数组中
- $first：根据资源文档的排序获取第一个文档数据
- $last：根据资源文档的排序获取最后一个文档数据
#### 1.2 $group
将集合中的文档分组，可用于统计结果，_id表示分组的依据，使用某个字段的格式为'$字段'
```
示例：统计男生、女生的总人数
db.stu.aggregate([
    {$group:
        {
            _id:'$gender',
            counter:{$sum:1}
        }
    }
])
```
Group by null 可以 将集合中所有文档分为一组
```
示例：求学生总人数、平均年龄
db.stu.aggregate([
    {$group:
        {
            _id:null,
            counter:{$sum:1},
            avgAge:{$avg:'$age'}
        }
    }
])
```
#### 1.3 $project
$project修改输入文档的结构，如重命名、增加、删除字段、创建计算结果  
```
例1：查询学生的姓名、年龄
db.stu.aggregate([
    {$project:{_id:0,name:1,age:1}}
])
例2：查询男生、女生人数，输出人数
db.stu.aggregate([
    {$group:{_id:'$gender',counter:{$sum:1}}},
    {$project:{_id:0,counter:1}}
])
```
## 二 MapReduce
#### 2.1 MapReduce简介
MapReduce相当于关系型数据库中的`group by`，使用MapReduce统计需要调用Map函数和Reduce函数，Map函数会调用emit(key,value)方法，此方法可以遍历集合中的所有记录，然后将key和value传递给reduce函数进行处理。MapReduce统计可以通过db.runCommand或者mapReduce命令执行，所以Map函数和Reduce函数可以使用JS来实现：
```
db.runCommand(
    {
        mapreduce: 目标集合名,
        map:    map函数,                            //映射函数，生成键值对序列，作为reduce函数参数
        reduce: reduce函数,                         //统计函数
        query: query函数                            //可选，用于目标记录过滤
        finalize: 最终处理函数
    }
);

map函数：
function(){
    emit(this.uid, 1)
}

reduce函数
function(k,v){
    var x = 0;
    v.forEach(function(){x += v});
    return x;
}
```