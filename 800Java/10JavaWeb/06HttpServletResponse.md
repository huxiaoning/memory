# HttpServletResponse

`HttpServletResponse`用来响应数据到浏览器的一个对象。



### 设置响应头

```java
        resp.setHeader("xxx", "aaa");
        resp.setHeader("xxx", "bbb"); // 会覆盖aaa
        resp.addHeader("yyy", "ccc");
        resp.addHeader("yyy", "ddd"); // 不会覆盖ccc
```



### 设置响应实体

```java
        // resp.sendError(404, "sorry");
        // resp.setHeader("content-type", "text/html;charset=utf-8"); // 解决响应乱码
        resp.setContentType("text/html;charset=utf-8"); // 解决响应乱码
        resp.getWriter().write("<b>this is resp body</b>");
        resp.getWriter().write("<b>今天天气真好，适合学习</b>");
```



### contentType有哪些

- `text/html;charset=utf-8`
  - 响应体为html格式的文档
- `text/plain;charset=utf-8`
  - 纯文本，html标签将不会被浏览器解析





### 解决请求乱码

##### 使用String类型进行重新编码(不分请求方式，但感觉有点性能问题)

```java
        String uname = req.getParameter("uname");
        // 浏览的编码方式为 ISO_8859_1
        // tomcat服务器的解码方式为 UTF_8
        // 将uname用ISO_8859_1解码，再用UTF_8编码即可
        String name = new String(uname.getBytes(StandardCharsets.ISO_8859_1), StandardCharsets.UTF_8);
```

##### 按请求方式的不同

- Get

  - `req.setCharacterEncoding(StandardCharsets.UTF_8.name());`

  - 编辑`server.xml`

    - ```xml
          <Connector port="8080" protocol="HTTP/1.1"
                     connectionTimeout="20000"
                     redirectPort="8443" useBodyEncodingForURI="true"/>
      ```

    - 添加的配置为`useBodyEncodingForURI="true"`

- Post

  - `req.setCharacterEncoding(StandardCharsets.UTF_8.name());`



