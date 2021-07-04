#  单表更新记录UPDATE

```sql
UPDATE [LOW_PRIORITY] [IGNORE] table_reference
SET col_name1={expr1|DEFAULT}
[,col_name2={expr2|DEFAULT}] ...
[WHERE where_condition];
```

```sql
UPDATE users SET age = age +5;
UPDATE users SET age = age - id,sex = 0;
UPDATE users SET age = age + 10 WHERE id % 2 = 0;
```

