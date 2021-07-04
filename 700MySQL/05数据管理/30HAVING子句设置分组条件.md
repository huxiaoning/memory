# HAVING子句设置分组条件

## `HAVING`

```sql
分组条件
[HAVING where_condition]
```

```sql
SELECT sex FROM users GROUP BY sex HAVING sex = '0';
```

