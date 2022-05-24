# MyBatis

### `MyBatis`简介

- 项目地址
  - 项目代码仓库放在[GitHub](https://github.com/mybatis/mybatis-3)。
- `MyBatis`是数据访问框架，底层是对JDBC的封装。
- 英文官方[文档](https://mybatis.org/mybatis-3/)。
- 中文官方[文档](https://mybatis.org/mybatis-3/zh/index.html)。

### 什么是 MyBatis？

MyBatis 是一款优秀的持久层框架，它支持自定义 SQL、存储过程以及高级映射。MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。





### 准备一个mysql数据库服务

```shell
$ docker run -it --name mysql -p 127.0.0.1:3306:3306 -e MYSQL_ROOT_PASSWORD=123456 -e MYSQL_DATABASE=mybatis -d --restart=always daocloud.io/mysql:5.7
```

连接信息如下：

- URL
  - `jdbc:mysql://127.0.0.1:3306/mybatis`
- USER
  - `root`
- PASSWORD
  - 123456

### 数据准备

```sql
DROP TABLE IF EXISTS t_user;
CREATE TABLE `t_user` (
  `id` INT(11) NOT NULL AUTO_INCREMENT COMMENT 'ID',
  `user_name` VARCHAR(100) NOT NULL COMMENT '用户名',
  `age` INT(11) DEFAULT NULL COMMENT '年龄',
  `sex` INT(11) DEFAULT NULL COMMENT '性别(0女1男)',
  `birth` DATE DEFAULT NULL COMMENT '出生日期',
  `mobile` VARCHAR(20) DEFAULT NULL COMMENT '手机号码',
  `create_user_id` INT(11) DEFAULT NULL COMMENT '创建用户的ID',
  `create_time` DATETIME DEFAULT NULL COMMENT '创建时间',
  `update_user_id` INT(11) DEFAULT NULL COMMENT '更新用户的ID',
  `update_time` DATETIME DEFAULT NULL COMMENT '更新时间',
  PRIMARY KEY (`id`)
) ENGINE=INNODB DEFAULT CHARSET=utf8;
SHOW CREATE TABLE t_user;

INSERT INTO t_user VALUES
(DEFAULT,'张三',12,1,'2020-10-15','13111111111',0,'2020-10-15 00:00:00',0,'2020-10-15 00:00:00'),
(DEFAULT,'李四',13,0,'2020-10-14','13111111113',0,'2020-10-15 00:00:00',0,'2020-10-15 00:00:00'),
(DEFAULT,'王五',14,1,'2020-10-13','13111111112',0,'2020-10-15 00:00:00',0,'2020-10-15 00:00:00'),
(DEFAULT,'赵六',15,1,'2020-10-12','13111111116',0,'2020-10-15 00:00:00',0,'2020-10-15 00:00:00');
```



### 导入`pom`依赖

```xml
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.9</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.49</version>
        </dependency>
```

### 下载源码和文档

```bash
$ mvn clean package
$ mvn dependency:sources
$ mvn dependency:resolve -Dclassifier=javadoc
```



### 入门
##### 1 创建实体对象`User`

```java
package org.example.entity;
import java.util.Date;
public class User {
    private Integer id;
    private String userName;
    private Integer age;
    private Integer sex;
    private Date birth;
    private String mobile;
    private Integer createUserId;
    private Date createTime;
    private Integer updateUserId;
    private Date updateTime;

	// 省略get set toString
}
```


##### 2 创建`mybatis`的XML配置文件`src/main/resources/mybatis-config.xml`

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://127.0.0.1:3306/mybatis?characterEncoding=utf-8"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="org/example/mapper/UserMapper.xml"/>
    </mappers>
</configuration>
```

##### 3 创建`mybatis`的XML映射配置文件`src/main/resources/org/example/mapper/UserMapper.xml`

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.example.mapper.UserMapper">
    <select id="findAll" resultType="org.example.entity.User">
        SELECT id,
               user_name      AS userName,
               age,
               sex,
               birth,
               mobile,
               create_user_id AS createUserId,
               create_time    AS createTime,
               update_user_id AS updateUserId,
               update_time    AS updateTime
        FROM t_user
    </select>

    <select id="get" resultType="org.example.entity.User">
        SELECT id,
               user_name      AS userName,
               age,
               sex,
               birth,
               mobile,
               create_user_id AS createUserId,
               create_time    AS createTime,
               update_user_id AS updateUserId,
               update_time    AS updateTime
        FROM t_user
        where id = 1
    </select>

    <select id="collect" resultType="org.example.entity.User">
        SELECT id,
               user_name      AS userName,
               age,
               sex,
               birth,
               mobile,
               create_user_id AS createUserId,
               create_time    AS createTime,
               update_user_id AS updateUserId,
               update_time    AS updateTime
        FROM t_user
    </select>
    <select id="count" resultType="int">
        select count(*)
        from t_user
    </select>
</mapper>
```

##### 4 查询用户列表

```java
package org.example;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.example.entity.User;
import java.io.IOException;
import java.io.InputStream;
import java.util.List;
import java.util.Map;

public class App {
    public static void main(String[] args) {
        String resource = "mybatis-config.xml";
        InputStream inputStream = null;
        try {
            inputStream = Resources.getResourceAsStream(resource);
        } catch (IOException e) {
            e.printStackTrace();
        }
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        SqlSession sqlSession = sqlSessionFactory.openSession();
        try {
            List<User> userList = sqlSession.selectList("org.example.mapper.UserMapper.findAll");
            System.out.println(userList);

            User user = sqlSession.selectOne("org.example.mapper.UserMapper.get");
            System.out.println(user);

            // 把数据库中哪个列的值，当作Map的key
            Map<Integer, User> map = sqlSession.selectMap("org.example.mapper.UserMapper.collect", "id");
            System.out.println(map);

            int count = sqlSession.selectOne("org.example.mapper.UserMapper.count");
            System.out.println(count);

        } finally {
            sqlSession.close();
        }
    }
}
```

