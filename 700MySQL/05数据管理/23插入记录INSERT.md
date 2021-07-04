# 插入记录INSERT

```sql
INSERT [INTO] table_name [(column_name,...)]
{VALUES|VALUE} ({expr | DEFAULT},...),(...),...
```

```sql
CREATE TABLE users(
	id SMALLINT UNSIGNED PRIMARY KEY auto_increment,
	username VARCHAR(20) NOT NULL,
	password VARCHAR(32) NOT NULL,
	age TINYINT UNSIGNED NOT NULL DEFAULT 10,
	sex BOOLEAN
);
-- 省略列名时所有字段都要一一赋值,用NULL或DEFAULT来支持id自增
INSERT INTO users VALUES(NULL,'Tom','123',25,1);
INSERT INTO users VALUES(NULL,'John','456',25,1);
INSERT INTO users VALUES(DEFAULT,'张三','456',25,1);
INSERT INTO users VALUES(DEFAULT,'张三','456',3*7-5,1);
-- DEFAULT也可以用来指定age字段使用其默认值
INSERT INTO users VALUES(DEFAULT,'张三','456',DEFAULT,1);
-- 一条语句插入多条记录
INSERT INTO users VALUES(DEFAULT,'张三','456',DEFAULT,1),(DEFAULT,'李四',md5('456'),DEFAULT,1);
```