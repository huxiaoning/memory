# `MySQL`创建数据表

## 打开数据库

```sql
USE 数据库名称；
```

```sql
USE test; -- 打开数据库test(准备在test库创建表)
SELECT DATABASE(); -- 查看下当前打开的是不是test库
```

## 创建数据库表

```sql
CREATE TABLE [IF NOT EXISTS] table_name(
    column_name data_type,
    ...
);
```

```sql
CREATE TABLE tb1(
    username VARCHAR(20),
    age TINYINT UNSIGNED,
    salary FLOAT(8,2) UNSIGNED
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='tb1表';
```

