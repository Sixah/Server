## 一 MySQL简介

#### 1.1 MySQL简单介绍

#### 1.2 MySQL语法

具体的sql语句中，可以使用 ; \g \G 三种形式表示语句结束，前两个表达的意思一样，只是结束标志，第三个表示格式化显示结果。  

SQL语言一共分为4大类：
- DDL：数据定义语言
- DML：数据操纵语言
- DQL：数据查询语言
- DCL：数据控制语言

#### 1.3 MySQL存储引擎

常见的存储引擎有：
- InnoDB：支持事务，所以占用空间大，适合频繁更新、删除的数据库
- MyISAM：不支持事务、外键，所以访问速度快
- MEMORY：使用内存存储数据，访问速度快，适合数据小、速度要求快的数据库

对引擎的查询与设置语句：
```
show engines;                               # 查看所有引擎
show variables like '%storage_engine%';     # 查看默认引擎	
```

修改默认引擎:在my.ini配置文件中修改：default-storage-engine 

## 二 MySQL数据类型

#### 2.1 整数

| 类型 | Singned/Unsigned | 存储 | 最小值 | 最大值 |
| :-: | :-: | :-: | :-: | :-: |
| TINYINT | Signed | 1字节 | -128 | 127 |
|  | Unsigned |  | 0 | 255 |
| SMALLINT | Signed | 2字节 | -32768 | 32767 |
|  | Unsigned | | 0 | 32767 |
| MEDIUMINT | Signed | 3字节 | -8388608 | 8388607 |
|  | Unsigned |  | 0 | 16777215 |
| INT | Signed | 4字节 | -2147483648 | 2147483647 |
|  | Unsigned |  | 0 | 4294967295 |
| BIGINT | Signed | 8字节 | -9223372036854775808 | 9223372036854775807 |
|  | Unsigned |  | 0 | 18446744073709551615 |

#### 2.2 小数

| 类型 | 存储 | 最小值 | 最大值 | 说明 |
| :-: | :-: | :-: | :-: | :-: |
| FLOAT | 4字节 |  |  | 单精度浮点数 |
| DOUBLE | 8字节 |  |  | 双精度浮点数 |
| DECIMAL | DECIMAL(M,D),M>D为M+2，否则为D+2 | 依赖于M和D的值 | 依赖于M和D的值 | 小数值 |

需要精确到小数点后10位以上，才会使用DOUBLE。精度要求非常高则可以使用位类型DEC和DECIMAL。FLOAT和DOUBLE存储的是近似值，DECIMAL存储的是字符串。

#### 2.3 时间类型

| 类型 | 存储 | 范围 | 格式 | 说明 |
| :-: | :-: | :-: | :-: | :-: |
| DATE | 3字节 | 1000-01-01/9999-12-31 | YYY-MM-DD | 日期值 |
| TIME | 3字节 |-838:59:59/838:59:59 | HH:MM:SS | 时间或持续 |
| YEAR | 1字节 | 1901/2155 | YYYY | 年份 |
| DATETIME | 8字节 | 1000-01-01 00:00:00 / 9999-12-31 23:59:59 | YYYY-MM-DD HH:MM:SS | 混合日期和时间值 |
| TIMESTAMP | 4字节 | 1970-01-01 00：00：00 / 2037年某时  | YYYYMMDD HHMMSS | 混合日期和时间戳 |

使用说明：
- 表示年月日，一般使用DATE类型
- 表示年月日分秒，使用DATETIME
- 经常插入或者更新日期为当前系统时间，使用TIMESTAMP
- 表示分秒，使用TIME
- 表示年份，使用YEAR

#### 2.4 字符串

| 类型 | 存储 |  说明 |
| :-: | :-: | :-: |
| CHAR | 0-255字节 | 定长字符串 |
| VARCHAR | 0-65535字节 | 变长字符串 |
| TINYBLOB | 0-255字节 | 不超过255个字符的二进制字符串 |
| TINYTEXT | 0-255字节 | 短文本字符串 |
| BLOB | 0-65 535字节 | 二进制形式的长文本数据 |
| TEXT | 0-65 535字节 | 长文本数据 |
| MEDIUMBLOB | 0-16 777 215字节 | 二进制形式的中等长度文本数据 |
| MEDIUMTEXT | 0-16 777 215字节 | 中等长度文本数据 |
| LONGBLOB | 0-4 294 967 295字节 | 二进制形式的极大文本数据 |
| LONGTEXT | 0-4 294 967 295字节 | 定长字符串 |

存储小数据，可以使用CHAR（很少变化选择该类型）或者VARCHAR（经常变化）

## 三 约束

约束分类：
```
主键	    primary key   性能最高，且唯一
非空	    not null
惟一	    unique
默认	    default
外键	    foreign key
自动递增    auto_increment
```

关于主键：一列或者一组列，其值能位移区分表中的每一行。  

没有主键，更新或者删除表中特定的行很困难，因为没有安全的方法保证只涉及相关的行。虽然并不总是需要主键，但是大多数数据库管理人员都会为每个表创建一个主键，以便于以后的数据管理。  

表中的任何列都可以作为主键，只要同时满足：任意两行都不具有相同主键值；每行都有一个主键值，且不为NULL。  

使用多列多为主键时，上述条件必须应用到构成主键的所有列，所有列的组合必须唯一。

多字段主键创建案例：使用constraint
```
create table teacher(
	name varchar(20),
	fathername varchar(20),
	sonname varchar(20),
	constraint pk_name_sonname primary key(name,sonname)
);
```

## 四 SQL语言

#### 3.1 数据控制语言 DCL

#### 3.2 数据定义语言 DDL

#### 3.3 数据操纵语言 DML

增加
全列插入：insert into 表名 values(值1,值2...)
缺省插入：insert into 表名(列1,...) values(值1,...)
同时插入多条数据：
insert into 表名 
values(值1,值2,值3...),
(值1,值2,值3...),
(值1,值2,值3...),
或者：	  insert into 表名(列1,...) values(值1,...),(值1,...)...;
注意：自动增加约束、默认值约束的字段可以不用插入数值。

插入查询语句：可以实现从一个表复制数据到另一个表
  insert into 表名(字段1,字段2,字段3...) select ......

删除
delete from 表名 where 条件 
逻辑删除，本质就=是修改update：alter table students add isdelete bit default 0;
如果需要删除则：update students isdelete=1 where ...;


修改
update 表名 set 列1=值1,... where 条件

#### 3.4 数据查询语言 DQL

select * from 表名

temp:
```
表中的数据库对象包括：列、索引、触发器。
查看当前数据库中所有表
show tables;
创建表
create table 表名(列及类型);
create table students(
id int auto_increment primary key not null,
name varchar(10) not null,
gender bit default 1,
birthday datetime
);
修改表
alter table 表名 add|change|drop 列名 类型;
alter table students add birthday datetime;
add	字段名 字段类型				在表的最后一个位置增加字段
add 字段名 字段类型 first			在表的第一个位置添加字段
add 字段名 字段类型 after 字段 	在指定字段后添加字段
drop 字段名						删除字段
modify 字段名 字段类型			修改字段类型
modify 字段名1 字段类型 first|after 字段名2 修改字段顺序
change 字段名 新字段名 字段类型	修改字段名
change 字段名 新字段名 新字段类型 修改字段名、字段类型

删除表
drop table 表名;
更改表名称
rename table 原表名 to 新表名;
查看表的创建语句
show create table '表名';
查看当前表中所有列：
show columns from 表名;
describe 表名;		//快速写法，也可以简写为：desc 表名;
```

## 五 常见问题解决

#### 5.1 命令行插入中文乱码

需要修改mysql配置文件并重启：
```
[mysql]
default-character-set=utf8
```

注意：即使客户端、服务端都是gbk的编码，存储的时候仍然是乱码，因为数据的传输中被编译成了utf8。
