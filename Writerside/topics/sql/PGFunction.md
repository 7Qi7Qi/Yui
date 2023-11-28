# 常用函数列表

## 数学函数



## 三角函数







## 字符串处理

### regexp_replace

1. 用途：正则匹配替换
2. 语法：``REGEXP_REPLACE (source_string, pattern [, replace_string [, position [, occurrence, [match_parameter] ] ] ])``

```sql
select no, regexp_replace( classification_name, '（\w+）', '\1', 'g' )  from erp_project_file_model_list
```


# 其他函数列表

## 加密、解密
+ 加密：encrypt(data bytea, key bytea, type text) 
+ 解密：decrypt(data bytea, key bytea, type text)

```sql
SELECT
	encode( encrypt ( 'abcd1234', '0123456789ABHAEQ', 'aes' ), 'base64' );
SELECT
	encode( decrypt ( decode( 'IlBruWclssA3y9oOsgMQpw==', 'base64' ), '0123456789ABHAEQ', 'aes' ), 'escape' )
```

## 编码、解码
+ encode(str, passwd_str)
+ decode(str, passwd_str)
+ 编码、解码方式：base64、hex、escape

------



[参考来源]: https://blog.csdn.net/sun5769675/article/details/50628979	"postgresql常用"