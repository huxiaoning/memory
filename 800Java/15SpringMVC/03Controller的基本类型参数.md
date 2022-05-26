# Controller的基本类型参数

```java
@Controller
public class DemoController {
    @RequestMapping("/demo")
    public String demo(String name, int age) {
        System.out.println("执行了springMVC的控制器");
        System.out.println("name = " + name);
        System.out.println("age = " + age);
        return "main.jsp";
    }
}
```

请求:

```
http://localhost:8080/demo?name=张三&age=20
```

