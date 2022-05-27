# Restful传值

```java
    @RequestMapping("/demo3/{id1}/{name1}")
    public String demo3(@PathVariable("id1") int id, @PathVariable("name1") String name) {
        System.out.println("id = " + id);
        System.out.println("name = " + name);
        return "/main.jsp";
    }
```

```
http://localhost:8080/demo3/4/zhangsan
```

