# SpringMVC



### Servlet路径配置

- `/`
  - 除`jsp`页面以外，所有请求都由当前Servlet处理
  - jsp页面正常显示
- `/*`
  - 除`jsp`页面以外，所有请求都由当前Servlet处理
  - jsp页面**不正常**显示





### SpringMVC中的重要组件

- `DispatcherServlet`
  - 前端控制器，接收所有请求(如果配置`/`不包含`jsp`)
- `HandlerMapping`
  - 解析请求格式，判断希望要执行哪个具体的方法。
- `HandlerAdapter`
  - 负责调用具体的方法
- `ViewResolver`
  - 视图解析器，解析结果，准备跳转到具体的物理视图。



