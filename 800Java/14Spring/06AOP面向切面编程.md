# AOP面向切面编程

Aspect Oriented Programming 面向切面编程。

正常程序执行流程都是自上而下纵向执行。

在程序原有纵向执行流程中，针对某一个或某一些方法添加通知，形成横切面过程就叫做**面向切面编程**.

常用概念

- 原有功能：**切点**，	cut point

- **前置通知**：在切点之前执行的功能．before advice

- **后置通知**：在切点之后执行的功能，after advice

- 如果切点执行过程中出现异常，会触发**异常通知**．throws advice

- 所有功能总称叫做**切面**．



### AOP两种方式

- **Schema-base**
  - `<aop:config>`
  - 每个通知都需要实现接口或类。
- **AspectJ**
  - `<aop:config><aop:aspect></aop:aspect></aop:config>`
  - 每个接口不需要实现接口或类。



### AOP方式一 - Schema-base示例(前置通知和后置通知)

- 定义两个切点方法

  - ```java
    public class Demo {
        public void demo() { // 切点
            System.out.println("demo()");
        }
        public void demo(String name, int age) { // // 切点
            System.out.println("demo(name,age)");
        }
    }
    ```

- 定义前置切面

  - ```java
    public class MyBeforeAdvice implements MethodBeforeAdvice {
        @Override
        public void before(Method method, Object[] args, Object target) throws Throwable {
            System.out.println("执行前置通知");
        }
    }
    ```

- 定义后置切面

  - ```java
    public class MyAfterAdvice implements AfterReturningAdvice 
        @Override
        public void afterReturning(Object returnValue, Method method, Object[] args, Object target) throws Throwable {
            System.out.println("执行后置通知");
        }
    }
    ```

- 切点配置

```xml
    <bean id="demo" class="org.example.aop.Demo"/> <!-- 切点Bean -->
    <bean id="myBeforeAdvice" class="org.example.advice.MyBeforeAdvice"/> <!-- 前置通知Bean -->
    <bean id="myAfterAdvice" class="org.example.advice.MyAfterAdvice"/> <!-- 后置通知Bean -->
 
    <aop:config><!-- aop切面 -->
        <aop:pointcut id="myPoint" expression="execution(* org.example.aop.Demo.demo())"/><!-- 切点 -->
        <aop:advisor advice-ref="myBeforeAdvice" pointcut-ref="myPoint"/><!-- 前置通知 -->
        <aop:advisor advice-ref="myAfterAdvice" pointcut-ref="myPoint"/><!-- 后置通知 -->
    </aop:config>
```



### 切点[表达式](https://www.cnblogs.com/zhangxufeng/p/9160869.html)

```xml
<aop:pointcut id="myPoint" expression="execution(* org.example.aop.Demo.demo())"/>
```

- `*` 通配符，匹配任意方法名、类名、一经包名 。(主要用于匹配单个单词，或者是以某个词为前缀或后缀的单词)
  - 拦截任意方法(限无参方法)
    - `<aop:pointcut id="myPoint" expression="execution(* org.example.aop.Demo.*())"/>`
  - 拦截任意类中的任意方法(不限制参数)
    - `<aop:pointcut id="myPoint" expression="execution(* org.example.aop.*.*(..))"/>`
  - 拦截任意子包下的Demo类中的任意方法
    - `<aop:pointcut id="myPoint" expression="execution(* org.example.*.Demo.*(..))"/>`
- `..` 通配符，该通配符表示0个或多个项，主要用于declaring-type-pattern和param-pattern中，如果用于declaring-type-pattern中，则表示匹配当前包及其子包，如果用于param-pattern中，则表示匹配0个或多个参数。
  - `execution(* com.spring.service..*.businessService())`
    - 匹配返回值为任意类型，并且是com.spring.service包及其子包下的任意类的名称为businessService的方法，而且该方法不能有任何参数：
  - `execution(* com.spring.service.BusinessObject.businessService(java.lang.String,..))`
    - 使用..表示任意个数的参数的示例，需要注意，表示参数的时候可以在括号中事先指定某些类型的参数，而其余的参数则由..进行匹配。



### 在通知中可以拿到什么

```java
public class MyBeforeAdvice implements MethodBeforeAdvice {
    @Override
    public void before(Method method, Object[] args, Object target) throws Throwable {
        System.out.println(method); // 切点方法
        for (Object arg : args) { // 参数列表
            System.out.println(arg);
        }
        System.out.println(target); // 切点实例
        System.out.println("执行前置通知");
    }
}
public class MyAfterAdvice implements AfterReturningAdvice 
    @Override
    public void afterReturning(Object returnValue, Method method, Object[] args, Object target) throws Throwable {
        System.out.println(returnValue); // 切点方法的返回值(void方法这里是null)
        System.out.println(method); // 切点方法
        for (Object arg : args) { // 参数列表
            System.out.println(arg);
        }
        System.out.println(target); // 切点实例
        System.out.println("执行后置通知");
    }
}
```





### 异常通知

没有

### 环绕通知

```java
public class Demo {
    public void demo() { // 切点
        System.out.println("demo()");
    }
}
```

```java
import org.aopalliance.intercept.MethodInterceptor;
import org.aopalliance.intercept.MethodInvocation;
public class MyArround implements MethodInterceptor {
    @Override
    public Object invoke(MethodInvocation invocation) throws Throwable {
        System.out.println("环绕 - 前置");
        Object proceed = invocation.proceed();
        System.out.println("环绕 - 后置");
        return proceed;
    }
}
```

```xml
    <bean id="demo" class="org.example.aop.Demo"/> <!-- 切点Bean -->
    <bean id="myArround" class="org.example.aop.advice.MyArround"></bean> <!-- 环绕通知Bean -->
    <aop:config><!-- 切面 -->
        <aop:pointcut id="myPoint" expression="execution(* org.example.aop.Demo.*(..))"/> <!-- 切点 -->
        <aop:advisor advice-ref="myArround" pointcut-ref="myPoint"/> <!-- 环绕通知 -->
    </aop:config>
```

