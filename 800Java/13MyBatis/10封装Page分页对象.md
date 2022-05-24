# 封装Page分页对象



```java
package org.example.dto;

import java.util.List;

/**
 * @author Jett
 */
public class PageDto<T> {
    /**
     * 入参:页码
     * 页面从1开始(不是从0开始)
     */
    private final int pageNum;
    /**
     * 入参:页面大小
     * rows - "LIMIT [offset,] rows;" 中的offset
     */
    private final int pageSize;
    /**
     * 由入参数计算得到
     * offset - "LIMIT [offset,] rows;" 中的offset
     */
    private final int pageStart;

    /**
     * 实体列表
     * 查询得到的结果
     */
    private List<T> entityList;

    /**
     * 总记录数
     * 查询得到的结果
     */
    private int total;
    /**
     * 总页码
     * 由查询结果 总记录数计算得出
     */
    private int totalPageNum;

    public PageDto(int pageNum, int pageSize) {
        this.pageNum = pageNum;
        this.pageSize = pageSize;
        this.pageStart = pageSize * (pageNum - 1);
    }

    public void setResult(List<T> entityList, int total) {
        this.entityList = entityList;
        this.total = total;
        this.totalPageNum = total % pageSize == 0 ? total / pageSize : total / pageSize + 1;
    }
}
```



### 使用

- `UserMapper`

  - ```java
    public interface UserMapper {
        List<User> find(@Param("page") PageDto<User> page, @Param("idList") List<Integer> idList);
        int count(@Param("page") PageDto<User> page, @Param("idList") List<Integer> idList);
    }
    ```

- `UserMapper.xml`

  - ```xml
    <mapper namespace="org.example.mapper.UserMapper">
        <select id="find" resultType="user">
            SELECT
            <include refid="columns"></include> <!-- 复用SQL片段 -->
            FROM t_user
            <where>
                <foreach collection="idList" item="id"
                         open="ID in (" separator="," close=")">
                    #{id}
                </foreach>
            </where>
            LIMIT #{page.pageStart}, #{page.pageSize}
        </select>
        <select id="count" resultType="int">
            SELECT
            count(*)<!-- 复用SQL片段 -->
            FROM t_user
            <where>
                <foreach collection="idList" item="id"
                         open="ID in (" separator="," close=")">
                    #{id}
                </foreach>
            </where>
        </select>
    </mapper>
    ```

- `UserMapperTest`

  - ```java
                UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
                PageDto<User> userPage = new PageDto<>(1, 3);
                List<Integer> idList = Arrays.asList(1, 2, 3, 4, 5);
                List<User> userList = userMapper.find(userPage, idList);
                int count = userMapper.count(userPage, idList);
                userPage.setResult(userList, count);
                System.out.println(userPage);
    ```

