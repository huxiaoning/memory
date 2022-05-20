# Session

### Session作用

共享一个用户不同请求间的数据共享。



### Session原理

         * 用户第一次请求时，写一个cookie给浏览器(自动的，不需要程序员手动创建)
         * Cookie: JSESSIONID=3868D005D7D26B90A87AEB51C269E35A
         * 用户后续请求，协带这个cookie



```java
@WebServlet("/sessionServlet")
public class SessionServlet extends HttpServlet {

    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.setCharacterEncoding("utf-8");
        resp.setContentType("text/html;charset=utf-8");
        /**
         * 如果是用户第一次请求服务器 || session对象过期失效了
         *  浏览不会协带Cookie JSESSIONID，这时候会创建一个全新的Session对象，并将这个Session对象的ID作为Cookie JSESSIONID响应给浏览器，并缓存Session对象
         * 如果不是第一次请求服务器
         *  浏览器会协带Cookie JSESSIONID,这时候会根据JSESSIONID去取出缓存的Session对象
         */
        HttpSession session = req.getSession(); // 如果没有调用这个方法，服务器将不会写Cookie JSESSIONID给浏览器。
        // 3868D005D7D26B90A87AEB51C269E35A,就是Cookie里面的JSESSIONID,果然没有Cookie就没有Session
        System.out.println(session.getId()); 
        session.setAttribute("", "");
        Object attribute = session.getAttribute("");
    }
}
```



### Session生命周期

一次会话。因为`Cookie(JSESSIONID)`会在浏览器关闭后失效。(但服务器内存中的session会在30分钟后回收)



### 如何注销Session

在`web.xml`中配置`session`的过期时间

```xml
    <session-config>
        <session-timeout>30</session-timeout> <!-- 单位(分钟) -->
    </session-config>
```

或者

```java
/**
指定 servlet 容器使该会话无效之前客户端请求之间的时间（以秒为单位）。
零或更小的间隔值表示会话永远不会超时。
参数：
间隔 - 指定秒数的整数(默认是30分钟即30*60s)
*/
session.setMaxInactiveInterval(int seconds); 
```

或者直接调用 HttpSession 的 invalidate() 方法, 该方法使 HttpSession 失效

 ```java
 session.invalidate();
 ```



##### 过期时间说明

过期时间是指，在指定的时间内，session对象没有被取出使用，即被认定为失效。

即用户关闭浏览器或者不再发起新的请求(getSession()),达到这个时间后，session失效。



### Session数据共享

```java
        session.setAttribute("", "");
        Object attribute = session.getAttribute("");
```



### Session失效处理

session失效后重定向到登录页面。

