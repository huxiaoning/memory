# MyBatis运行原理

- `Resources`

  - MyBatis中的IO流工具类
  - 用来加载配置文件到输入流

- `SqlSessionFactoryBuilder`

  - 用来构建SqlSessionFactory接口的实现类DefaultSqlSessionFactory

- `XMLConfigBuilder`

  - 将xml配置文件产生的输入流解析为Configuration对象

- `SqlSessionFactory`

  - 创建SqlSession的工厂类

- `Configuration`

  - 全局配置文件所有的配置信息。

- `DefaultSqlSessionFactory`

  - SqlSessionFactory接口的实现类

- `Transaction`

  - 事务类
  - 每个SqlSession会持有一个Transaction对象。

- `Environment`

  - 对应`mybatis-config.xml`中的<environment>标签

- `TransactionFactory`

  - 事务工厂，负责生产Transaction

- `Executor`

  - MyBatis执行器

  - 负责执行SQL命令。

  - 相当于JDBC中的statement、PreparedStatement或CallableStatement对象。

  - 默认的执行器都是`SimpleExecutor`

  - 可以指定批量执行器 `BatchExecutor`

    - `SqlSession sqlSession = sqlSessionFactory.openSession(ExecutorType.BATCH);`指定执行器

    - ```java
      package org.apache.ibatis.session;
      public enum ExecutorType {
        SIMPLE, REUSE, BATCH
      }
      ```

- `DefaultSqlSession`

  - `SqlSession`接口的实现类

- `ExceptionFactory`

  - 异常工厂

- `ErrorContext`

  - 错容器上下文。