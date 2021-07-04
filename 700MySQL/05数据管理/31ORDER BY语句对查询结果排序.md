# ORDER BY语句对查询结果排序

## `ORDER BY`

```sql
对查询结果进行排序
[ORDER BY {col_name|expr|position} [ASC|DESC],...]
```

```sql
SELECT * FROM users ORDER BY id DESC; -- 降序
SELECT * FROM users ORDER BY age DESC,id DESC; -- 如果年龄一样，再按id来隆序排序
```

