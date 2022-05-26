# scope属性

- **singleton**
  - 单例(默认)
- **prototype**
  - 原型多例

- request
  - 每次请求重新实例化
- session
  - 每个会话期间内，对象是单例的
- application
  - 在application对象内是单例
- global session
  - spring推出的一个对象，依赖于spring-webmvc-portlet

