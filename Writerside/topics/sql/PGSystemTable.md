# 常用系统表

关于postgresql系统表，可以参考[PostgreSQL 8.1 中文文档-系统表](http://www.php100.com/manual/PostgreSQL8/catalogs.html)。

**pg_class**

记录了数据库中的表，索引，序列，视图（"关系"）。

其中比较重要字段有：

- relname 表，索引，视图等的名字。
- relnamespace 包含这个关系的名字空间（模式）的     OID，对应pg_namespace.oid
- relkind r = 普通表，i = 索引，S = 序列，v = 视图， c     = 复合类型，s = 特殊，t = TOAST表

**pg_namespace**

记录了数据库的名字空间（模式）

其中比较重要的字段有：

- nspname 名字空间的名字
- nspowner 名字空间的所有者

**pg_attribute**

记录了数据库关于表的字段的信息。

其中比较重要的字段有：

- attrelid 此列/字段所属的表，对应于pg_class.oid
- attname 字段名字
- atttypid 这个字段的数据类型,对应于pg_type.oid
- attlen 对于定长类型，typlen是该类型内部表现形式的字节数目。     对于变长类型，typlen 是负数。 -1 表示一种"变长"类型（有长度字属性的数据）， -2 表示这是一个 NULL 结尾的     C 字串。是本字段类型 pg_type.typlen 的拷贝。
- attnum 字段数目。普通字段是从 1 开始计数的。系统字段， 比如     oid， 有（任意）正数。
- atttypmod atttypmod 元组在创建表的时候     提供的类型相关的数据（比如，一个 varchar 字段的最大长度）。 它传递给类型相关的输入和长度转换函数当做第三个参数。 其值对那些不需要     atttypmod 的类型而言通常为 -1。
- attnotnull 这代表一个非空约束。我们可以改变这个字段以打开或者关闭这个约束。
- attisdropped 这个字段已经被删除了，不再有效。

*注意：*

1. 如果字段类型为变长类型（如varchar）,那么在atttypmod中存储的长度比实际长度多4。可见[参考文档1](http://blog.163.com/digoal@126/blog/static/163877040201351743331312/)。
2. 如果字段类型为numeric，那么可通过atttypmod获得长度、精度等信息，具体方式可见[参考文档2](http://blog.163.com/digoal@126/blog/static/163877040201371763839672/)。

**pg_type**

记录了数据库有关数据类型的信息。

其中比较重要的字段有：

- typname 数据类型名字
- typlen 对于定长类型，typlen是该类型内部表现形式的字节数目。     对于变长类型，typlen 是负数。 -1 表示一种"变长"类型（有长度字属性的数据）， -2 表示这是一个 NULL 结尾的     C 字串。

**pg_description**

记录了数据库中对象（表、字段等）的注释。

其中比较重要的字段有：

- objoid 这条描述所描述的对象的     OID。如果这条注释是一个表或表中字段的注释，那么，该值对应于pg_class.oid
- objsubid 对于一个表字段的注释，它是字段号，对应于pg_attribute.attnum。对于其它对象类型，它是零。
- description 作为对该对象的描述的任意文本

**查询用户表**

```sql
SELECT 
	A.oid,
	A.relname AS NAME,
	b.description AS COMMENT 
FROM
	pg_class
	A LEFT OUTER JOIN pg_description b ON b.objsubid = 0 
	AND A.oid = b.objoid 
WHERE
	A.relnamespace = ( SELECT oid FROM pg_namespace WHERE nspname = 'public' ) --用户表一般存储在public模式下
	AND A.relkind = 'r' 
ORDER BY
	A.relname
```

**使用表名查询表字段的定义**

```sql
SELECT 
	A.attnum,
	A.attname AS field,
	T.typname AS TYPE,
	A.attlen AS LENGTH,
	A.atttypmod AS lengthvar,
	A.attnotnull AS NOTNULL,
	b.description AS COMMENT 
FROM
	pg_class C,
	pg_attribute
	A LEFT OUTER JOIN pg_description b ON A.attrelid = b.objoid 
	AND A.attnum = b.objsubid,
	pg_type T 
WHERE
	C.relname = 'zc_zclx' 
	AND A.attnum > 0 
	AND A.attrelid = C.oid 
	AND A.atttypid = T.oid 
ORDER BY
	A.attnum
```

**使用表oid查询表字段的定义**

```sql
SELECT 
	A.attname AS field,
	T.typname AS TYPE,
	A.attlen AS LENGTH,
	A.atttypmod AS lengthvar,
	A.attnotnull AS NOTNULL,
	b.description AS COMMENT 
FROM
	pg_attribute
	A LEFT OUTER JOIN pg_description b ON A.attrelid = b.objoid 
	AND A.attnum = b.objsubid,
	pg_type T 
WHERE
	A.attnum > 0 
	AND A.attrelid = 162903 
	AND A.atttypid = T.oid 
ORDER BY
	A.attnum
```



**参考文档**

1. [PostgreSQL 9.0 modify      pg_attribute.atttypmod extend variable char length avoid rewrite table](http://blog.163.com/digoal@126/blog/static/163877040201351743331312/)
2. [PostgreSQL      How can i decode the NUMERIC precision and scale in pg_attribute.atttypmod](http://blog.163.com/digoal@126/blog/static/163877040201371763839672/)