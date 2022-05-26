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
import org.example.pojo.Person;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
@Controller
public class DemoController {
    @RequestMapping("/demo")
    public String demo(Person person, String name, int age,
                       HttpServletRequest req,
                       HttpServletResponse resp,
                       HttpSession session
    ) {
        System.out.println("执行了springMVC的控制器");
        System.out.println("person = " + person);
        System.out.println("name = " + name);
        System.out.println("age = " + age);
        return "main.jsp";
    }
}
```

包括请求、响应和会话对象，都可以成功注入。





### 参数注解

`@RequestParam`

```java
    @RequestMapping("/page")
    public String page(
            @RequestParam(value = "pageNumber", defaultValue = "1") int pageNum,
            @RequestParam(value = "pageCount", defaultValue = "10", required = false) int pageSize
    ) {
        System.out.println("执行了springMVC的控制器");
        System.out.println("pageNum = " + pageNum);
        System.out.println("pageSize = " + pageSize);
        return "main.jsp";
    }
```

