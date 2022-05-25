# AOP方式二AspectJ



- 引入依赖

  - ```xml
            <dependency>
                <groupId>org.aspectj</groupId>
                <artifactId>aspectjweaver</artifactId>
                <version>1.9.9.1</version>
            </dependency>
            <dependency>
                <groupId>org.aspectj</groupId>
                <artifactId>aspectjrt</artifactId>
                <version>1.9.9.1</version>
            </dependency>
    ```

- 切点类

  - ```java
    public class Demo {
        public void demo() { // 切点
            System.out.println("demo()");
        }
        public int demo(String name, int age) { // // 切点
            System.out.println("demo(name,age)");
            return age;
        }
    }
    ```

- 通知类

  - ```java
    import org.aspectj.lang.ProceedingJoinPoint;
    public class MyAdvice {
        public void before() {
            System.out.println("before()");
        }
        public void after() {
            System.out.println("after()");
        }
        public void afterReturning() {
            System.out.println("afterReturning()");
        }
        public void afterThrowing(Throwable t) {
            System.out.println("afterThrowing()");
            System.out.println(t.getMessage());
        }
        public Object myArround(ProceedingJoinPoint p) throws Throwable {
            System.out.println("myArround()");
            System.out.println("环绕前置");
            Object proceed = p.proceed();
            System.out.println("环绕后置");
            return proceed;
        }
    }
    ```

- aop配置方式二

  - ```xml
        <bean id="demo" class="org.example.aop.Demo"/> <!-- 切点Bean -->
        <bean id="myAdvice" class="org.example.aop2.MyAdvice"/> <!-- 通知Bean -->
        <aop:config>
            <aop:aspect ref="myAdvice"> <!-- 切面配置 -->
                <aop:pointcut id="myPoint" expression="execution(* org.example.aop.Demo.*(..))"/> <!-- 切点 -->
                <aop:before method="before" pointcut-ref="myPoint"/> <!-- 前置通知 -->
                <aop:after method="after" pointcut-ref="myPoint"/> <!-- 后置通知 -->
                <aop:after-returning method="afterReturning" pointcut-ref="myPoint"/> <!-- 方法返回后置通知 -->
                <aop:after-throwing method="afterThrowing" pointcut-ref="myPoint" throwing="t"/> <!-- 异常通知 -->
                <aop:around method="myArround" pointcut-ref="myPoint"/>
            </aop:aspect>
        </aop:config>
    ```



注意：

一般都是在service包下配置切点来做声明式事务，所以在**service的方法不要处理异常**，否则，无法收到异常通知，无法回滚事务。



### `<aop:after>`与`<aop:after-returning>`的区别

- 执行顺序
  - 看在spring xml中配置的先后顺序。
- 执行时机有所不同
  - `aop:after` 无论是否报异常，都会收到通知并执行
  - `aop:after-returning` 发生异常的时候，不会执行，只有正常退出的方法才会通知`aop:after-returning`



### `<aop:after>`与`<aop:after-throwing>`的区别与上述类似





### 获取切点参数

```xml
    <bean id="demo" class="org.example.aop.Demo"/> <!-- 切点Bean -->
    <bean id="myAdvice" class="org.example.aop2.MyAdvice"/> <!-- 通知Bean -->
    <aop:config>
        <aop:aspect ref="myAdvice"> <!-- 切面配置 -->
            <aop:pointcut id="myPoint" expression="execution(* org.example.aop.Demo.*(String,int)) and args(name1,age1)"/> <!-- 切点 -->
            <aop:before method="before" pointcut-ref="myPoint" arg-names="name1,age1"/> <!-- 前置通知 -->
        </aop:aspect>
    </aop:config>
```

```java
public class Demo {
    public int demo(String name, int age) { // // 切点
        System.out.println("demo(name,age)");
        return age;
    }
}
```

```java
public class MyAdvice {
    public void before(String name1, int age1) {
        System.out.println("before() - " + name1 + age1);
    }
}
```

优点是能传参数了，缺点是表达式通配方法参数能力弱。

