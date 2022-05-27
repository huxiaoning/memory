# SpringMVC运行原理

- `DispatcherServlet`
  - 接收除jsp外的所有请求
  - 如果配置了静态资源处理，静态资源请求不接收
  - 收到的请求交由`HandlerMapping`解析
- `HandlerMapping`
  - 解析请求
  - 解析完后交由`HandlerAdapter`处理
  - `class org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping`
- `HandlerAdapter`
  - 根据上一步解析结果调用具体的控制器
  - `class org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter`
- `Controller`
- `ViewResolver`
  - 解析控制想要转发或重定向的视图。

