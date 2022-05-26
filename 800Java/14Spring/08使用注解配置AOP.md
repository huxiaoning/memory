# 使用注解配置AOP

基于Aspect。

- 启用注解扫描

  - ```xml
        <context:component-scan base-package="org.example"/> <!-- 扫描多个包用逗号隔开 -->
        <!--
         proxy-target-class
            true:使用cglib动态代理
            false:使用jdk动态代理
         -->
        <aop:aspectj-autoproxy proxy-target-class="true"/>
    ```

- 注入切点Bean

  - ```java
    @Component
    public class Demo {
        @Pointcut("execution(* org.example.aop.Demo.demo())")
        public void demo() { // 切点
            System.out.println("demo()");
        }
    }
    ```

- 注入切面通知Bean

  - ```java
    import org.aspectj.lang.ProceedingJoinPoint;
    import org.aspectj.lang.annotation.*;
    import org.springframework.stereotype.Component;
    @Component
    @Aspect
    public class MyAdvice {
        @Before("org.example.aop.Demo.demo()")
        public void before() {
            System.out.println("before()");
        }
    
        @After("org.example.aop.Demo.demo()")
        public void after() {
            System.out.println("after()");
        }
    
        @AfterReturning("org.example.aop.Demo.demo()")
        public void afterReturning() {
            System.out.println("afterReturning()");
        }
    
        @AfterThrowing(value = "org.example.aop.Demo.demo()", throwing = "t")
        public void afterThrowing(Throwable t) {
            System.out.println("afterThrowing()");
            System.out.println(t.getMessage());
        }
    
        @Around("org.example.aop.Demo.demo()")
        public Object myAround(ProceedingJoinPoint p) throws Throwable {
            System.out.println("myArround()");
            System.out.println("环绕前置");
            Object proceed = p.proceed();
            System.out.println("环绕后置");
            return proceed;
        }
    }
    ```

- 测试代码

  - ```java
    public class App {
        public static void main(String[] args) {
            ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
            Demo demo = context.getBean(Demo.class);
            demo.demo();
        }
    }
    ```



