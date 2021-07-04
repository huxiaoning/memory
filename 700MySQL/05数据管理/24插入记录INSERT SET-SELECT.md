# 插入记录INSERT SET-SELECT

## `INSERT SET`

```sql
INSERT [INTO] table_name 
SET column={expr|DEFAULT},...;
说明：与第一种插入数据方式的区别在于，此方法可以使用子查询(SubQuery)。
但一次只能插入一条记录。
```

```sql
INSERT users SET username='Ben',password='345';
```

## `INSERT SELECT`

```sql
INSERT [INTO] table_name [(column_name,...)] SELECT ...
此方式可以将SELECT查询结果插入到指定数据表。
```

