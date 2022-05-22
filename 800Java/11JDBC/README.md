# JDBC

### 准备一个mysql数据库服务

```shell
$ docker run -it --name mysql -p 127.0.0.1:3306:3306 -e MYSQL_ROOT_PASSWORD=123456 -e MYSQL_DATABASE=jdbc -d --restart=always daocloud.io/mysql:5.7
```

连接信息如下：

- URL
  - `jdbc:mysql://127.0.0.1:3306/jdbc`
- USER
  - `root`
- PASSWORD
  - 123456



### 弄一些自测数据

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





### Idea创建`Maven` `quick-start`工程

`maven-archetype-quickstart `

### 引入mysql驱动依赖jar包

```xml
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.49</version>
        </dependency>
```



### 用JDBC操作数据库

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
public class DbUtil {
    public static final String URL = "jdbc:mysql://127.0.0.1:3306/jdbc?characterEncoding=utf-8";
    public static final String USER = "root";
    public static final String PASSWORD = "123456";
    private static Connection conn = null;
    static {
        try {
            Class.forName("com.mysql.jdbc.Driver");
            conn = DriverManager.getConnection(URL, USER, PASSWORD);
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        }
    }
    public static Connection getConnection() {
        return conn;
    }
}
```



```java
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

    // 省略get and set
}
```

```java
import org.example.entity.User;
import org.example.util.DbUtil;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;
public class UserDao {
    public void save(User user) {
        String sql = "INSERT INTO t_user VALUES (DEFAULT,?,?,?,?,?,?,?,DEFAULT,DEFAULT)";
        Connection conn = DbUtil.getConnection();
        try {
            PreparedStatement ps = conn.prepareStatement(sql);
            ps.setString(1, user.getUserName());
            ps.setInt(2, user.getAge());
            ps.setInt(3, user.getSex());
            ps.setDate(4, new java.sql.Date(user.getBirth().getTime()));
            ps.setString(5, user.getMobile());
            ps.setInt(6, user.getCreateUserId());
            ps.setDate(7, new java.sql.Date(user.getCreateTime().getTime()));
            ps.execute();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public void update(User user) {
        String sql = "UPDATE t_user SET user_name = ?,age = ?,sex = ?,birth = ?,mobile = ?,update_user_id = ?,update_time = ? WHERE id = ?";
        Connection conn = DbUtil.getConnection();
        try {
            PreparedStatement ps = conn.prepareStatement(sql);
            ps.setString(1, user.getUserName());
            ps.setInt(2, user.getAge());
            ps.setInt(3, user.getSex());
            ps.setDate(4, new java.sql.Date(user.getBirth().getTime()));
            ps.setString(5, user.getMobile());
            ps.setInt(6, user.getUpdateUserId());
            ps.setDate(7, new java.sql.Date(user.getUpdateTime().getTime()));
            ps.setInt(8, user.getId());
            ps.execute();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public void del(int id) {
        String sql = "DELETE FROM t_user WHERE id = ?";
        Connection conn = DbUtil.getConnection();
        PreparedStatement ps = null;
        try {
            ps = conn.prepareStatement(sql);
            ps.setInt(1, id);
            ps.execute();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public List<User> queryAll() {
        String sql = "SELECT id,user_name,age,sex,birth,mobile,create_user_id,create_time,update_user_id,update_time FROM t_user";
        Connection conn = DbUtil.getConnection();
        try {
            PreparedStatement ps = conn.prepareStatement(sql);
            ResultSet rs = ps.executeQuery();
            List<User> userList = new ArrayList<>();
            while (rs.next()) {
                User user = new User();
                // 使用下标的方式：下标从1开始
                // user.setId(rs.getInt(1));
                user.setId(rs.getInt("id"));
                user.setUserName(rs.getString("user_name"));
                user.setAge(rs.getInt("age"));
                user.setSex(rs.getInt("sex"));
                user.setBirth(rs.getDate("birth"));
                user.setMobile(rs.getString("mobile"));
                user.setCreateUserId(rs.getInt("create_user_id"));
                user.setCreateTime(rs.getDate("create_time"));
                user.setUpdateUserId(rs.getInt("update_user_id"));
                user.setUpdateTime(rs.getDate("update_time"));
                userList.add(user);
            }
            return userList;
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return new ArrayList<>();
    }

    public User get(int id) {
        String sql = "SELECT id,user_name,age,sex,birth,mobile,create_user_id,create_time,update_user_id,update_time FROM t_user WHERE id = ?";
        Connection conn = DbUtil.getConnection();
        PreparedStatement ps = null;
        try {
            ps = conn.prepareStatement(sql);
            ps.setInt(1, id);
            ResultSet rs = ps.executeQuery();
            if (rs.next()) {
                User user = new User();
                // 使用下标的方式：下标从1开始
                // user.setId(rs.getInt(1));
                user.setId(rs.getInt("id"));
                user.setUserName(rs.getString("user_name"));
                user.setAge(rs.getInt("age"));
                user.setSex(rs.getInt("sex"));
                user.setBirth(rs.getDate("birth"));
                user.setMobile(rs.getString("mobile"));
                user.setCreateUserId(rs.getInt("create_user_id"));
                user.setCreateTime(rs.getDate("create_time"));
                user.setUpdateUserId(rs.getInt("update_user_id"));
                user.setUpdateTime(rs.getDate("update_time"));
                return user;
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return null;
    }
}
```

##### 单元测试类

```java
import org.example.entity.User;
import org.junit.Test;

import java.util.Date;
import java.util.List;

public class UserDaoTest {
    @Test
    public void saveTest() {
        UserDao userDao = new UserDao();
        User user = new User();
        user.setUserName("王大炮");
        user.setAge(20);
        user.setSex(1);
        user.setBirth(new Date());
        user.setMobile("18022222222");
        user.setCreateUserId(1);
        user.setCreateTime(new Date());
        userDao.save(user);
    }
    @Test
    public void updateTest() {
        UserDao userDao = new UserDao();
        User user = new User();
        user.setId(10);
        user.setUserName("刘大炮");
        user.setAge(22);
        user.setSex(1);
        user.setBirth(new Date());
        user.setMobile("18022222222");
        user.setUpdateUserId(1);
        user.setUpdateTime(new Date());
        userDao.update(user);
    }
    @Test
    public void delTest() {
        UserDao userDao = new UserDao();
        userDao.del(1);
    }
    @Test
    public void queryAllTest() {
        UserDao userDao = new UserDao();
        List<User> users = userDao.queryAll();
        System.out.println(users);
    }
    @Test
    public void getTest() {
        UserDao userDao = new UserDao();
        User user = userDao.get(3);
        System.out.println(user);
    }
}
```



### 注意

- sql参数赋值只能使用索引方式，不能指定字段名，索引从1开始。
- 查询结果集`ResultSet`可以使用两种取值方式
  - 索引 - 索引从1开始 `user.setId(rs.getInt(1));`
  - 字段名 - `user.setId(rs.getInt("id"));`



