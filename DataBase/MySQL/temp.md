事务的问题：
- 脏读：一个事务读取到了另外一个事务未提交数据
- 虚读：即不可重复读，在同一个事务中，两次读取到的数据不一样
- 幻读：一个事务操作数据表中所有记录，另一个事务添加了一个数据，则前者查询不到自己的修改

隔离级别：
- read uncommitted：读未提交，会产生脏读 不可重复读 幻读
- read committter：读已提交，会产生不可重复度 幻读。Oracle默认隔离级别。
- reapeatable read：可重复度，会产生幻读。MySQL默认隔离级别
- serializebale：串行化，可所有问题

演示脏读（也包含不可重复度，因为窗口B在同一事务中查询到了不同的结果）：
```
# 假设uid1和uid2的账户都是1000元
# 窗口A                                                    # 窗口B
set global transaction read uncommitted;                    select * from account;      # 得到二者都是1000元
start transaction;                                          start transaction;
-- 转账操作 -- 
update accoutn set balance=balance-500 where uid=1;
update accoutn set balance=balance+500 where uid=2;         
                                                            select * from account;      # uid1为500，uid2为1500，读取了未提交的数据
roolback;
                                                            # 两边此时拿到的数据已经不一样了
                                    
```

提升一档事务的隔离级别，重复上述操作，窗口B查询到的结果永远都是1000，不会读取到窗口A的事务，避免了脏读。但是当窗口A提交后，窗口B的事务还是读取到了另外一个事务的数据，即不可重复度的现象还在。此时可以继续提升事务的隔离级别。