# 数据库管理

## `MySql`语句规范

```
关键字与函数名称全部大写
数据库名称、表名称、字段名称全部小写
SQL语句必须以分号结尾
```

## 创建数据库

```sql
CREATE {DATABASE | SCHEMA} [IF NOT EXISTS] db_name
[DEFAULT] CHARACTER SET [=] charset_name;
```

```sql
CREATE DATABASE db1;
SHOW DATABASES;
CREATE DATABASE IF NOT EXISTS db1;
SHOW WARNINGS;
SHOW CREATE DATABASE db1; -- 查看创建数据库db1的sql,包括数据库编码信息
CREATE DATABASE IF NOT EXISTS db2 CHARACTER SET gbk;
```

## 修改数据库库

```sql
ALTER {DATABASE | SCHEMA} db_name
[DEFAULT] CHARACTER SET [=] charset_name;
```

```sql
ALTER DATABASE db2 CHARACTER SET = utf8;
SHOW CREATE DATABASE db2; -- 发现变成utf8编码了
```

## 删除数据库

```sql
DROP {DATABASE | SCHEMA} [IF EXISTS] db_name;
```

```sql
DROP DATABASE db1;
SHOW DATABASES; -- 发现数据库db1已经被删除了。
DROP DATABASE IF EXISTS db2;
```

## 常用

```sql
SHOW DATABASES;
CREATE DATABASE IF NOT EXISTS db CHARACTER SET utf8;
ALTER DATABASE db CHARACTER SET = utf8;
SHOW CREATE DATABASE db;
DROP DATABASE IF EXISTS db;
```

