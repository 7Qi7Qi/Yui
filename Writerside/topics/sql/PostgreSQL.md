# PostgreSQL

> [diagoal blog](https://github.com/digoal/blog)
> [PostgreSQL & MySQL](https://www.zhihu.com/question/31955622)
> [PostgreSQL 事故分析](https://liujiacai.net/blog/2022/02/24/hairy-pg-incident-lessons/)

----

## DataType

### 数组

#### ANY()




## Keyword

### LIKE

#### escape 
> 使用``escape``关键字定义转义符。
```sql
select * from table_a where a.name like '%5/%%' escape '/'
```
> ``/``是转义字符，第二个``%``为普通字符，第一、第三个为通配符








------

## Misc

### CTE

1. **Common Table Expression**
2. 通用表表达式
3. 声明CTE的需要使用语法**WITH**
4. 可以当作一个为查询而存在的临时表，类似使用子查询时的派生表，可以用在insert、update等
5. MySQL 8.0支持

### 隔离级别

1. 查看指令：show transaction isolation level
    1. 默认级别：读已提交 Read-Committed
    2. [其他详见](MySQL.md#隔离界别)

## Page

| 分页方法         | 耗时   | 业务排序 | 业务过滤 | 跳转指定页 | 是否精确 |
|--------------|------|------|------|-------|------|
| LIMIT-OFFSET | 线性增加 | √    | √    | √     | √    |
| Keyset       | 一般   | √    | √    | ×     | √    |
| xmin         | 快    | ×    | √    | √     | ×    |
| ctid         | 很快   | ×    | ×    | √     | √    |
| pg_stats     | 快    | √    | ×    | √     | ×    |

