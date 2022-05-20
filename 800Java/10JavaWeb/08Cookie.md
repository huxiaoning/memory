# Cookie

### 服务器响应cookie给浏览器

```java
@WebServlet("/cookieServlet")
public class CookieServlet extends HttpServlet {

    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.setCharacterEncoding("utf-8");
        resp.setContentType("text/html;charset=utf-8");

        resp.addCookie(new Cookie("a", "1")); // 浏览器下次请求服务器就会带上这个cookie
        resp.addCookie(new Cookie("b", "2")); // 浏览器下次请求服务器就会带上这个cookie
        resp.getWriter().write("Cookie 学习");
    }
}
```



服务器响应cookie给浏览器后，浏览器就会记住这个cookie，下次请求这个服务器将自动带上这些cookie。

### 服务器读取浏览器带上的cookie

```java
@WebServlet("/cookie2Servlet")
public class Cookie2Servlet extends HttpServlet {

    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.setCharacterEncoding("utf-8");
        resp.setContentType("text/html;charset=utf-8");
        
        Cookie[] cookies = req.getCookies();
        if (cookies != null) {
            for (Cookie cookie : cookies) {
                System.out.println(cookie.getName() + " --> " + cookie.getValue());
            }
        }

        resp.getWriter().write("Cookie2 学习");
    }
}

a --> 1
JSESSIONID --> 0D6C913C644F05275E889CBD3A04CD5C
_ga --> GA1.1.835850808.1623414864
```





### cookie特点：

- 浏览器端的数据存储技术
- 存储的数据声明在服务器端
- 默认cookie信息存储好之后，每次请求都会附带，除非设置有效路径。



### 设置cookie有效期

```java
cookie.setMaxAge(3 * 24 * 60 * 60); // 设置cookie有效期为3天,默认不设置的话为-1表示不存储
```

```
/**
设置此 Cookie 的最大期限（以秒为单位）。
正值表示 cookie 将在经过这么多秒后过期。请注意，该值是 cookie 过期的最大期限，而不是 cookie 的当前期限。
负值表示 cookie 不会永久存储，并且会在 Web 浏览器退出时被删除。零值会导致 cookie 被删除。
参数：
expiry -- 一个整数，以秒为单位指定 cookie 的最长期限；如果为负，则表示不存储 cookie；如果为零，则删除 cookie
 */
public void setMaxAge(int expiry) {
    maxAge = expiry;
}

/**
获取此 Cookie 的最大年龄（以秒为单位）。
默认返回-1 ，表示cookie会一直持续到浏览器关闭。
返回：
一个整数，以秒为单位指定 cookie 的最大年龄；如果为负，则表示 cookie 一直存在到浏览器关闭
 */
public int getMaxAge() {
    return maxAge;
}
```

### 设置有效路径



```java
cookie2.setPath("/web-demo/cookie2Servlet"); // 设置有效路径，浏览在请求指定路径时才会协带此cookie,不会所有请求都协带。
```



### 三天免登录

