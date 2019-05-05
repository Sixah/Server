## 一 MySQL介绍

#### 1.1 MySQL简介

#### 1.2 MySQL连接

连接mysql命令
```
mysql -uroot -p								# -u后是数据库用户 -p表示需要输入密码，
mysql -h 192.168.5.116 -P 3306 -u root -p123456				# 远程连接
```
连接成功后，会提示`connection id`，该id也是连接的数量，每个新连接都会+1。

#### 1.3 MySQL的sql语法规范

具体的sql语句中，可以使用 ; \g \G 三种形式表示语句结束，前两个表达的意思一样，只是结束标志，第三个表示格式化显示结果。  

sql语句不区分大小写，但是推荐sql中的关键字都使用大写。  


## 二 DDL与DML语句

#### 2.1 常见DDL语句

```
create database test;						# 创建一个名为test的数据库
show databases;								# 查询系统中有哪些已创建的数据库

use test;									# 使用数据库test
show tables;								# 展示当前数据库中的所有表
create table user(							# 创建一个user表，字段有name age
	name varchar(10),
	age int(2)
);	
desc user;									# 查看user表的表结构			
show create table user;						# 查看user表建表语句

drop database test;							# 删除数据库
drop table user;							# 删除表

alter table user rename users;				# 修改表名
alter table user add colum sex int(2);		# 增加字段
alter table user drop colum sex;			# 删除字段
alter table user modify name varchar(20);	# 修改字段定义
alter table user change name username varchar(20);	# 修改字段名，change也可以修改定义

```

#### 2.2 常见DML语句

```
# 插入记录
insert into user(name) values('lisi');		# 未声明字段使用默认值
insert into user values('zs', 20);			# 依次赋值

# 多行插入
insert into user(name,age) values ('lisi',30),('zs',20);

# 更新记录
update user set age=30 where name='lisi';	# 将名字为lisi的人age设置为20

# 更新多表记录
update user u, order o set u.update_time=o.create_time where u.uid=o.uid;

# 删除记录
delete from user where name='lisi';

# 删除夺标记录
delete u,o from user u, order o where u.uid=o.uid and u.uid=100071;

# 查询
select * from user;
```

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

