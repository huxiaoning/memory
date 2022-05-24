# 封装MyBatis工具类

```java
package org.example.util;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import java.io.IOException;
import java.io.InputStream;
public class MyBatisUtil {
    private static SqlSessionFactory sqlSessionFactory;

    static {
        // 初始化SqlSessionFactory
        String resource = "mybatis-config.xml";
        try {
            InputStream inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    private static final ThreadLocal<SqlSession> localSqlSession = new ThreadLocal<>();

    public static SqlSession getSqlSession() {
        SqlSession sqlSession = localSqlSession.get();
        if (sqlSession != null) {
            return sqlSession;
        }
        SqlSession newSqlSession = sqlSessionFactory.openSession();
        localSqlSession.set(newSqlSession);
        return newSqlSession;
    }

    public static void releaseSession() {
        SqlSession sqlSession = localSqlSession.get();
        if (sqlSession == null) {
            return;
        }
        sqlSession.close();
        localSqlSession.remove();
    }

    public static void main(String[] args) {
        // 这段测试代码逻辑应该写在Web过滤器里面,这个过滤器的名称叫OpenSessionInView
        SqlSession sqlSession = MyBatisUtil.getSqlSession(); // 1
        try {
            // chain.doFilter(request, response); // 放行
            sqlSession.commit(); // 2
        } catch (Exception e) {
            sqlSession.rollback(); // 3
            e.printStackTrace();
        } finally {
            MyBatisUtil.releaseSession();  // 4
        }
    }
}
```

