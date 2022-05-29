# cookie跨域

### 浏览器如何存储`cookie`

- 浏览器访问`localhost:8080`
- 服务器响应cookie给浏览器
  - 服务器调用`HttpSession session = request.getSession();`，该方法内部的逻辑如下：
    - 如果请求没有协带`cookie[JSESSIONID]`，或者协带的`cookie[JSESSIONID]`已经过期失效：
      - 创建新的session对象，并把session对象的id当作`cookie[JSESSIONID]`响应给客户端
    - 如果请求协带的`cookie[JSESSIONID]`是有效的：
      - 直接返回这个有效的session对象
- 浏览器存储cookie的结构如下：
  - <domain,cookie>



如果浏览器访问:`127.0.0.1:8080`,将不会协带这个cookie,这个服务器会重新给浏览响应一个新的cookie。



结论就是默认**`cookie`并不能跨域**。

浏览器并不会把百度的`cookie`带给新浪。



### `cookie`跨域支持

```java
cookie.setDomain(".test.com.cn");
```

这样可以跨如下的域名，协带此`cookie`

- `www.test.com.cn`
- `sso.test.com.cn`
- `.test.com.cn`
- `test.com`



`cookie[JSESSIONID]`是`getSession`方法内部维护的，我们程序员无法干涉，使其支持跨域，

但我们可以另外创建一个`cookie`,如:`cookie[custom_global_session_id]`

然后模仿Servlet的Session机制，写一套全局的Session机制。

