# 动态SQL

根据不同的条件需要执行不同的SQL命令.称为动态SQL。

### 动态SQL标签

- **if**
  - 多个if之间没有else的关系，满足条件就会串入sql条件，n个if就可能有n个条件。

- **choose**、**when**、**otherwise**
  - choose类似于swich带break,只会有一个条件。

- trim、**where**、**set**
- **foreach**
- script
- bind
- 插入脚本语言



##### 与jstl类似，但有区别:

- jstl标签中取作用域数据要用EL表达式`${name}`

- mybatis作用域动态标签中取map参数，不能使用#{paramName},如下是一个正确的示例：

  - `idList`，这种直接写属性名的表达式称为**`OGNL`表达式**。
  
  - ```xml
        <select id="page" resultType="user" parameterType="map">
            SELECT id,
            user_name AS userName,
            age,
            sex,
            birth,
            mobile,
            create_user_id AS createUserId,
            create_time AS createTime,
            update_user_id AS updateUserId,
            update_time AS updateTime
            FROM t_user
            WHERE id IN
            <!-- idList ： 这是OGNL表达式的写法，不要写成#{idList} -->
            <foreach collection="idList" item="id" open="(" separator="," close=")">
                #{id}
            </foreach>
            LIMIT
            #{pageStart}
            , #{pageSize}
        </select>
    ```
  
  - ```java
    List<User> page(@Param("idList") List<Integer> idList, @Param("pageStart") int pageStart, @Param("pageSize") int pageSize);
    ```
  
  - 除了`<foreach>`标签外，其他标签也一样，标签属性中取参数，不用#{}，更不用${}，但标签内容部分，还是要加的，如`#{id}`。



##### trim

- prefix 在前面添加内容
- prefixOverrides 去掉前面内容
- suffix 在后面添加内容
- suffixOverrieds 去掉后面内容
- 执行顺序：先去掉内容，后添加内容。

##### bind

定义临时变量的。



##### foreach

- IN查询(主要)

- 批量intert

  - ```java
    SqlSession sqlSession = sqlSessionFactory.openSession(ExecutorType.BATCH);
    ```

  - 底层使用JDBC的`PreparedStatement.addBatch()`





### 定义SQL片段

```xml
    <sql id="columns"> <!-- 定义SQL片段 -->
        id,
        user_name AS userName,
        age,
        sex,
        birth,
        mobile,
        create_user_id AS createUserId,
        create_time AS createTime,
        update_user_id AS updateUserId,
        update_time AS updateTime
    </sql>

    <select id="page" resultType="user" parameterType="map">
        SELECT
        <include refid="columns"></include> <!-- 复用SQL片段 -->
        FROM t_user
        WHERE id IN
        <foreach collection="idList" item="id" open="(" separator="," close=")">
            #{id}
        </foreach>
        LIMIT
        #{pageStart}
        , #{pageSize}
    </select>
```

多表联合查询的时候用的比较多。
