# MySQL


## 隔离界别

1. 查看指令：``show variables like 'transaction_isolation'``
   1. 默认是 可以重复读 ``Repeatable-Read``
   2. 推荐使用 读已提交 ``Read Committed``
2. 

> 默认可重复读原因:
> 那Mysql在5.0这个版本以前，binlog只支持STATEMENT这种格式！
> 而这种格式在读已提交(Read Committed)这个隔离级别下主从复制是有bug的
> 因此Mysql将可重复读(Repeatable Read)作为默认的隔离级别！
## 索引


### 索引失效
1. 当使用左或左右模糊匹配时，也就是 ``like %xx`` 或者 ``like %xx%``。
2. 查询条件中对索引列使用函数。
3. 查询条件中对索引列使用表达式计算
4. MySQL在遇到字符串和数字比较时，会自动把字符串转为数字，然后再比较。
   1. 如果索引列是字符串，而条件输入参数是数字的话，那么索引列会发生隐式类型转换
   2. 隐式类型转换是通过CAST函数实现，等同于对索引列使用函数
5. 联合索引要能正确使用，需要遵循最左匹配原则，即按照最左优先的方式进行索引匹配，否则索引失效。
6. where字句中，如果OR前面的条件列是索引列，但OR后的条件列不是索引列，那么索引会失效，全表扫描。


## 语句

### truncate、drop、delete 对比
1. truncate和drop是ddl语句，执行无法回滚；delete是dml语句，可以回滚
2. truncate只能作用于表；delete和drop可以作用于表、视图等
3. truncate会清空表内所有行，但保留表结构及其约束、索引等；drop会删除表结构及其约束索引等
4. truncate会重置表的自增值；delete不会
5. truncate不会激活触发器；delete会
6. truncate后会使得表和索引占的空间释放恢复；delete不会；drop语句会将表占用空间全部释放

## 案例

### 一、线上执行update语句修改数据库时，where条件没有带上索引，导致业务直接崩了

1. 前提：
   1. 基于InnoDB存储引擎,隔离级别是可重复读
2. 原因：
   1. 这种隔离级别下，多个事务并发时，会出现幻读问题，即同一个事务下，连续两次执行同个查询语句，第二次的查询可能会返回之前没有的行
   2. 因此InnoDB存储引擎实现了行锁，通过next-key锁（记录锁和间隙锁的组合）来锁住记录本身和记录之间的间隙，防止其他事务在这个记录之间插入新的记录，从而避免了幻读现象
   3. 执行update语句时，实际上会对记录加独占锁（X锁），如果其他线程修改该条记录会被阻塞，事务结束后释放（非update语句执行完）
   4. InnoDB事务中，对记录加锁的基本单位是next-key锁，但在一些条件下，会退化成间隙锁或记录锁。
      1. where条件加唯一索引
      2. 如果where条件没有加锁，就会全表扫描，所有记录添加next-key锁，相当于锁表
      3. 除了``select ... from`` 语句，其他语句都会被锁住不能执行
      4. 最终要看优化器选择的是索引扫描还是全表扫描，可以使用``force index([index name])``
   5. 加锁的位置在索引上而非行上
3. 解决：
   1. sql_safe_update设置为1
   2. update语句需要满足如下条件之一才会执行成功
      1. 使用where，且where条件必须带有索引列
      2. 使用limit
      3. 同时使用where和limit，此时where可以没有索引列
   3. delete语句
      1. 使用where，且where条件必须带有索引列
      2. 同时使用where和limit，此时where可以没有索引列
4. [链接](https://mp.weixin.qq.com/s/9R8ChusahrJvLGmUvHWBgA)



### 二、如何实现跨库的联表查询

1. 在同一个服务器下的两个不同的逻辑数据库。表前面加数据库名即可
```sql
 select * from orders o left join userdb.user u on o.uid = u.id
```
2. 在不同的服务器下的不同数据库
   1. ``show engines``
      1. 添加 ``federated``
      2. 需要 ``federated  YES``
   2. 远程数据库表映射到本地数据库，类似oracle的dblink
   ```sql
   create table `orders` (
   ...
   ) ENGINE = FEDERATED AUTO_INCREMENT=1 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci
   CONNECTION = 'mysql://root:123456@127.0.0.1:3306/orderdb/orders'
   ```
   3. 注意事项
      1. 不推荐使用，老项目过渡可以
      2. 本地表结构必须和远程完全一样
      3. 远程数据库只支持连接MySQL
      4. 不支持事务
      5. 不支持表结构修改
      6. 本地更新，远程表也会更新，反之亦然
      7. 删除本地表。远程表不会删除

----



## 函数

## 字符串

#### 字符串截取

1. SUBSTRING
   1. SUBSTRING(s, start, length)
   2. 从字符串s的start位置截取长度length的字符串
2. LEFT
   1. LEFT(s,n)
   2. 返回字符串s的后前个字符
3. RIGHT：同上
4. MID：
   1. MID(s, n, len)
   2. 从字符串s的n位置截取长度为len的字符串
   3. 同SUBSTRING(s, n, len)

#### 聚合函数

1. GROUP_CONCAT
   1. GROUP_CONCAT([DISTINCT] field [order by field])
   2.   字符串聚合函数，可去重，可加排序，与group by连用

#### 其他

1. RLIKE

   1. RLIKE 'pattern'
   2. REGEXP执行字符串表达式模式匹配
   3. ^ 开始、$ 结束、| 或者、

   ```sql
   select 
       * 
   from 
       patients 
   where 
       conditions rlike '^DIAB1|.*\\sDIAB1$'
   ```

   
