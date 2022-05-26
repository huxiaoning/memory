# Controller的对象类型参数



```java
public class Person {
    private String name;
    private int age;
	// get set toString
}
```



```java
@Controller
public class DemoController {
    @RequestMapping("/demo")
    public String demo(Person person) {
        System.out.println("执行了springMVC的控制器");
        System.out.println("person = " + person);
        return "main.jsp";
    }
}
```

请求:

```
http://localhost:8080/demo?name=张三&age=20
```





# 对象类型与基本类型参数混合使用

```java
    @RequestMapping("/demo")
    public String demo(Person person, String name, int age) {
        System.out.println("执行了springMVC的控制器");
        System.out.println("person = " + person);
        System.out.println("name = " + name);
        System.out.println("age = " + age);
        return "main.jsp";
    }
```

都可以成功注入。

