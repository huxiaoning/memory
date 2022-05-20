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
        // resp.setHeader("content-type", "text/html;charset=utf-8");
        resp.setContentType("text/html;charset=utf-8");
        resp.getWriter().write("<b>this is resp body</b>");
        resp.getWriter().write("<b>今天天气真好，适合学习</b>");
```



### contentType有哪些

- `text/html;charset=utf-8`
  - 响应体为html格式的文档
- `text/plain;charset=utf-8`
  - 纯文本，html标签将不会被浏览器解析