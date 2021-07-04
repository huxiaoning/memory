# 创建带有多个OUT类型参数的存储过程

创建一个存储过程，根据年龄删除用户，返回删除的用户和剩余的用户

```sql
--  得到插入、更新、删除后被影响的记录总数
SELECT ROW_COUNT();

INSERT INTO test (username) VALUES ('D'),('E'),('F');
SELECT ROW_COUNT(); -- 输出：3

-- https://zhuanlan.zhihu.com/p/103283746
select version(), @@sql_mode;
SET sql_mode=(SELECT REPLACE(@@sql_mode,'ONLY_FULL_GROUP_BY',''));


DROP PROCEDURE IF EXISTS removeUserByAgeAndReturnInfos;

CREATE PROCEDURE removeUserByAgeAndReturnInfos
(
	IN p_age SMALLINT UNSIGNED,
	OUT deleteUserCount SMALLINT UNSIGNED,
	OUT overplusUserCount SMALLINT UNSIGNED
)
BEGIN
	DELETE FROM users WHERE age = p_age;
	SELECT ROW_COUNT() INTO deleteUserCount;
	SELECT COUNT(*) FROM users INTO overplusUserCount;
END;

CALL removeUserByAgeAndReturnInfos(20,@deleteUserCount,@overplusUserCount);
SELECT @deleteUserCount,@overplusUserCount;
```

