# Controller的复杂参数

### 复选框参数

```
@RequestMapping("/demo1")
public String demo1(@RequestParam(value = "hobby") List<String> hobbies) {
    for (String hobby : hobbies) {
        System.out.println(hobby);
    }
    return "main.jsp";
}
```

```
http://localhost:8080/demo1?hobby=学习&hobby=看视频
```



### 参数名中带点

```java
    @RequestMapping("/demo2")
    public String demo2(Demo demo) {
        System.out.println(demo);
        return "main.jsp";
    }
public class Demo {
    private Person person; 
    // get set toString
}
public class Person {
    private String name;
    private int age;
    // get set toString
}
```

```
http://localhost:8080/demo2?person.name=张三&age=20
```



#### 再复杂一点

```java
    @RequestMapping("/demo2")
    public String demo2(Demo demo) {
        System.out.println(demo);
        return "main.jsp";
    }
public class Demo {
    private List<Person> person;
    // get set toString
}
public class Person {
    private String name;
    private int age;
    // get set toString
}
```



```
<form action="/demo2" method="post">
    <input name="person[0].name" value="张三"><br>
    <input name="person[0].age" value="20"><br>
    <input name="person[1].name" value="李四"><br>
    <input name="person[1].age" value="21"><br>
    <button type="submit">提交</button>
    <br>
</form>
```

