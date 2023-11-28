# Postgres Statement

## System Statement

##### 一、查询字段和注释

```sql
SELECT
	col_description ( A.attrelid, A.attnum ) AS COMMENT,
	format_type ( A.atttypid, A.atttypmod ) AS TYPE,
	A.attname AS NAME,
	A.attnotnull AS NOTNULL 
FROM
	pg_class AS C,
	pg_attribute AS A 
WHERE
	C.relname = 'erp_niche' 
	AND A.attrelid = C.oid 
	AND A.attnum >0
	AND A.atttypid > 0
```
+ A.attnum > 0 过滤标识符
    + xid(事务标识符)
    + oid(标识数据库对象，如数据库、库、视图等)
    + cid(命令标识符)
    + tid(行标识符)
+ A.atttpid > 0 过滤 ........pg.dropped.xxx........
##### 二、查询正在运行的SQL语句

```sql
SELECT
	* 
FROM
	pg_stat_activity 
WHERE
	STATE = 'active'
```

##### 三、查询表的用户权限

```sql
SELECT
	* 
FROM
	information_schema.table_privileges 
WHERE
	TABLE_NAME = 'erp_entry'
```

##### 四、查询行名称

```sql
SELECT
	* 
FROM
	information_schema.columns 
WHERE
	TABLE_NAME = 'erp_project'
```

##### 五、终止sql

```sql
select pid, query from pg_stat_activity where state = 'active';
select pg_terminate_backend(pid);
```

##### 六、系统Schema

```sql
select * from information_schema.columns --tables
```

七、查看表大小、索引大小

```sql
select pg_size_pretty(pg_relation_size('ys_oa_flow_parking_apply_detail'))
```

```sql
SELECT 
	TABLE_NAME,
	pg_size_pretty ( table_size ) AS table_size,
	pg_size_pretty ( indexes_size ) AS indexes_size,
	pg_size_pretty ( total_size ) AS total_size 
FROM
	(
	SELECT 
		TABLE_NAME,
		pg_table_size ( TABLE_NAME ) AS table_size,
		pg_indexes_size ( TABLE_NAME ) AS indexes_size,
		pg_total_relation_size ( TABLE_NAME ) AS total_size 
	FROM
		( SELECT ( '"' || table_schema || '"."' || TABLE_NAME || '"' ) AS TABLE_NAME FROM information_schema.tables ) AS all_tables 
	ORDER BY
		total_size DESC 
) AS pretty_sizes
```






------

### Custom Function example

##### 一、处理json

```sql
CREATE OR REPLACE FUNCTION handle_json ( word VARCHAR ) RETURNS VARCHAR AS $body$ DECLARE
	ret VARCHAR;
BEGIN
	ret := word;
	ret = REPLACE ( ret, '"', '' );
	ret = REPLACE ( ret, '[', '' );
	ret = REPLACE ( ret, ']', '' );
	ret = REPLACE ( ret, 'null', '' );
	IF
		ret = '' THEN
			RETURN NULL;
	END IF;
	RETURN ret;
END;
$body$ LANGUAGE plpgsql;
```

```sql
SELECT
	project_type :: json -> 'traditionalType',
	project_type,
	handle_json ( ( project_type :: json -> 'traditionalType' ) :: VARCHAR ) 
FROM
	"erp_supplier_team" 
WHERE
	project_type != '' 
	AND company_no = '10';
```

##### 二、遍历表匹配某字段

```sql
CREATE 
	OR REPLACE FUNCTION som_f ( _tb1 regclass, field VARCHAR, val VARCHAR ) RETURNS VARCHAR AS $func$ DECLARE
	ret VARCHAR;
BEGIN
	EXECUTE format ( 'select (EXISTS (select 1 from %s where %s = ''%s''))', _tb1, field, val ) INTO ret;
	RETURN ret;
END;
$func$ LANGUAGE plpgsql;
```

```sql
SELECT
	p1.relname,
	som_f ( p1.oid, 'project_no', 'Asg19115' ) 
FROM
	pg_class p1
	JOIN pg_attribute p2 ON p1.oid = p2.attrelid 
WHERE
	p1.relkind = 'r' 
	AND p2.attnum > 0 
	AND p2.attname = 'project_no'
```




