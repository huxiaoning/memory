# LIMIT语句限制查询数量

## `LIMIT`

```sql
限制查询结果返回的数量
[LIMIT {[offset,] row_count|row_count OFFSET offset}]
```

```sql
SELECT * FROM users LIMIT 0,2;
SELECT * FROM users LIMIT 2 OFFSET 0; -- 与上面一条是等价的
```

