# 使用INSERT … SELECT 插入记录

## 准备一张数据表

```sql
CREATE TABLE tdb_goods_cates(
	cate_id SMALLINT UNSIGNED PRIMARY KEY AUTO_INCREMENT,
	cate_name VARCHAR(40) NOT NULL
);
```

## 将查询结果写入数据表

```sql
INSERT [INTO] table_name [(col_name,...)]
SELECT ...
```

```sql
INSERT INTO tdb_goods_cates (cate_name)
SELECT goods_cate FROM tdb_goods GROUP BY goods_cate
```

