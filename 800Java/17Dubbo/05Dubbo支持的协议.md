# Dubbo支持的协议

- Dubbl协议
  - 官方推荐的协议
  - 使用NIO和线程池进行处理
  - 缺点：大文件传输时可能出现文件传输失败的问题
- RMI协议
  - JDK提供的协议，远程方法调用协议
  - 缺点：偶尔连接失败
  - 优点：JDK原生，不需要进行额外配置(导入jar)
- Hession协议
  - 优点:基于http协议，htt请求支持
  - 缺点:需要额外导入jar，并在短连接时性能低。