# 创建带有IN和OUT类型参数的存储过程

从users表中删除id不固定的记录，并返回剩余的记录数。

```sql
DROP PROCEDURE IF EXISTS removeUserAndReturnUserNums;

CREATE PROCEDURE removeUserAndReturnUserNums(IN p_id SMALLINT(5) UNSIGNED,OUT userNums INT UNSIGNED)
BEGIN
	DELETE FROM users WHERE id = p_id;
	SELECT COUNT(*) FROM users INTO userNums;
	SELECT userNums;
END


CALL removeUserAndReturnUserNums(2,@nums);
SELECT @nums;

SELECT COUNT(*) FROM users;
```

## 局部变量声明

在BEGIN...END之间，且写在第一行

```sql
DECLARE username VARCHAR(255);
```

```sql
DROP PROCEDURE IF EXISTS aaa;

CREATE PROCEDURE aaa(OUT user_name VARCHAR(255))
BEGIN
	DECLARE username VARCHAR(255);
	SET username = '你好！';
	SELECT username INTO user_name;
END;


CALL aaa(@username);
SELECT @username;
```

