# cglib动态代理

- 优点
  - 基于字节码，生成真实对象的子类。
    - 运行效率高高于jdk动态代理
  - 不需要实现接口
- 缺点
  - 需要引入第三方jar包。







### 示例代码

```java
public class Boss {
    public void talkAboutBusiness() {
        System.out.println("谈个小目标");
    }
    public void eat() {
        System.out.println("吃饭");
    }
}
```



```java
import net.sf.cglib.proxy.Enhancer;
import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;
import java.lang.reflect.Method;
public class SecretaryCglib implements MethodInterceptor {
    /**
     * @param proxy       代理对象
     * @param method      被代理的方法
     * @param args        参数
     * @param methodProxy 代理方法
     * @return
     * @throws Throwable
     */
    @Override
    public Object intercept(Object proxy, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
        System.out.println("预约");
        Object invoke = methodProxy.invokeSuper(proxy, args); // 当代理接口时，不能调用invokeSupper方法，因为接口的方法是抽象的。
        System.out.println("备忘");
        return invoke;
    }
    public static void main(String[] args) {
        Enhancer enhancer = new Enhancer();
        enhancer.setSuperclass(Boss.class);
        enhancer.setCallback(new SecretaryCglib());
        Boss proxy = (Boss) enhancer.create();
        proxy.talkAboutBusiness();
        proxy.eat();
    }
}
```



使用spring aop时，只要出现Proxy和真实对象转换异常。

- 设置为true使用cglib动态代理
- 设置为fase使用jdk动态代理(默认)

