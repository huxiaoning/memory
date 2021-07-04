# 由 NOT IN EXISTS 引发的子查询

## 由[NOT] IN/EXISTS引发的子查询

### [NOT] IN语法结构

```sql
operand comparison_operator [NOT] IN (subquery)
=ANY 运算符与IN等效。
!=ALL或<>ALL运算符与NOT IN 等效。
```

### [NOT] EXISTS

如果子查询返回任何行，EXISTS将返回TRUE;否则返回FALSE。

```sql
SELECT EXISTS
	( SELECT * FROM tdb_goods );
SELECT EXISTS
	( SELECT * FROM tdb_goods WHERE goods_cate='1');
```

