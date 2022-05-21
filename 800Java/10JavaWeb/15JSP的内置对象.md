# JSP的内置对象

- PageContext对象

  - pageContext 或者 _jspx_page_context
  - 页面上下文对象，这个对象里面封装了其他八个对象。
  - 每次请求页面时候都会创建一个新的PageContext对象

- Request 对象

  - request

- Session 对象

  - session

- Application(ServletContext)对象

  - application

- Response 对象

  - response

- Out 对象

  - out
  - 响应对象，Jsp内部使用。带有缓冲区的响应对象，效率高于response对象。

- Page(Servlet)对象

  - page 相当于 this (当前转译的Servlet对象,也表示当前这个jsp页面)

- Exception 对象

  - 异常对象。存储了当前运行的异常信息。

  - 注意：使用此对象需要在page指定中使用属性isErrorPage＝＂true＂开启。`<%@ page isErrorPage="true" %>`这样jsp转译的Servlet文件中就定义了exception对象。

  - ```java
        java.lang.Throwable exception = org.apache.jasper.runtime.JspRuntimeLibrary.getThrowable(request);
        if (exception != null) {
          response.setStatus(javax.servlet.http.HttpServletResponse.SC_INTERNAL_SERVER_ERROR);
        }
    ```

- ServletConfig

  - config
  - 也就是ServletConfig，主要是用来获取web.xml中的配置数据，完成一些初始化数据的读取。




### 什么是内置对象

摘录转译Servlet的_jspService方法的部分代码：

```java
  public void _jspService(HttpServletRequest request, HttpServletResponse response){
    final javax.servlet.jsp.PageContext pageContext;
    javax.servlet.http.HttpSession session = null;
    final javax.servlet.ServletContext application;
    final javax.servlet.ServletConfig config;
    javax.servlet.jsp.JspWriter out = null;
    final java.lang.Object page = this;
    javax.servlet.jsp.JspWriter _jspx_out = null;
    javax.servlet.jsp.PageContext _jspx_page_context = null;

    try {
      pageContext = _jspxFactory.getPageContext(this, request, response,
      			null, true, 8192, true);
      _jspx_page_context = pageContext;
      application = pageContext.getServletContext();
      config = pageContext.getServletConfig();
      session = pageContext.getSession();
        = pageContext.getOut();
      _jspx_out = out;
    } catch (java.lang.Throwable t) {
    } finally {
    }
  }
```

这些在转译Servlet文件中自动声明的九个对象，在jsp中无需再次声明，可以在局部代码块中直接使用。

这些就是jsp的内置对象。



### 四个作用域对象

| 对象        | 作用域                 | 说明                                                         |
| ----------- | ---------------------- | ------------------------------------------------------------ |
| pageContext | 当前页面               | 仅当前页面(多次转发是不同的pageContext页面),用来获取其它内置对象。 |
| request     | 一次请求               | 可以在一次请求的多次转发间流转数据                           |
| session     | 一次会话               |                                                              |
| application | 项目内，应用启动到关闭 | 全局唯一 ServletContext                                      |





### JSP路径问题

```jsp
<%@ page contentType="text/html;charset=UTF-8" %>
<%
    // /web-demo
    String path = request.getContextPath();
    // http://localhost:8080/web-demo/
    String basePath = request.getScheme() + "://" + request.getServerName() + ":" + request.getServerPort() + path + "/";
%>
<html>
<head>
    <base href="<%=basePath%>"> <!-- 这个href会自动拼接到下面的a标签的href中 -->
    <title>学习JSP动态引入</title>
</head>
<body>
<a href="a.jsp">a.jsp</a>
</body>
</html>
```

