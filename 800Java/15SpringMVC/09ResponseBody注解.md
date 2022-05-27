# ResponseBody注解

### 不指定视图，使用响应文本流。

```java
    @RequestMapping("/demo6")
    public void demo6(HttpServletResponse resp) throws IOException {
        PrintWriter writer = resp.getWriter();
        writer.write("hello");
        writer.flush();
    }
```



### 使用@ResponseBody注解实现上述功能

```xml
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.13.3</version>
        </dependency>
```

```java
    @RequestMapping(value = "/demo6", produces = {"application/json;charset=utf-8"})
    @ResponseBody
    public Person demo6() {
        return new Person("张三", 20);
    }
```

- 在方法上只有＠RequestMapping时，无论方法返回值是什么(void除外)认为需要跳转。
- 添加@ResponseBody后
  - 设置响应头Content-Type为`application/json`
    - 这里没给我加编码(据然也没有乱码)，使用`produces = {"application/json;charset=utf-8"}`重新设置一下响应头
  - 响应体转成json格式
  - 并且不跳转页面



小结:ajax这种异步请求，对于后端来说，需要设置不跳转，并返回文本流即可。

