# jdk动态代理

分类:

- JDK
- cglib



### JDK动态代理

- 优点
  - JDK自带，不需要额外引入第三方jar包
- 缺点
  - 真实对象必须实现接口
  - 利用反射机制，效果不高 (感觉不是什么问题，生成的代理对象缓存起来就不需要反射了，只反射一次，问题不大)



### 示例代码

```java
public interface Func {
    void talkAboutBusiness();
    void eat();
}
public class Boss implements Func {
    @Override
    public void talkAboutBusiness() {
        System.out.println("谈个小目标");
    }
    @Override
    public void eat() {
        System.out.println("吃饭");
    }
}
public class SecretaryJdk implements InvocationHandler {
    private Func boss = new Boss();
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("预约");
        Object invoke = method.invoke(boss, args);
        System.out.println("备忘");
        return invoke;
    }
    public static void main(String[] args) {
        Func proxy = (Func) Proxy.newProxyInstance(Func.class.getClassLoader(), new Class[]{Func.class}, new SecretaryJdk());
        proxy.talkAboutBusiness();
        proxy.eat();
    }
}
```



mybatis中的Mapper接口使用的JDK动态代理。

