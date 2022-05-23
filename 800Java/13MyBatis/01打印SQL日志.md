# 打印SQL日志

### 1 引入log4j依赖`pom.xml`

```xml
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
```

### 2 配置`src/main/resources/log4j.properties`

```properties
# 全局开启ERROR级别日志(debug日志不输出)
log4j.rootCategory=ERROR,CONSOLE,LOGFILE

# 指定命名空间下的日志级别为DEBUG(下面的包或类路径下的debug日志可以输出)
# "org.example.mapper.UserMapper"
log4j.logger.org.example.mapper.UserMapper=DEBUG
# "org.example.App"
log4j.logger.org.example.App=DEBUG

log4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender
log4j.appender.CONSOLE.layout=org.apache.log4j.PatternLayout
log4j.appender.CONSOLE.layout.ConversionPattern=- %m%n

log4j.appender.LOGFILE=org.apache.log4j.FileAppender
log4j.appender.LOGFILE.layout=org.apache.log4j.PatternLayout
log4j.appender.LOGFILE.layout.ConversionPattern=%-4r [%t] %-5p %c %x - %m%n 
log4j.appender.LOGFILE.File=axis.log
log4j.appender.LOGFILE.Append=true
```

### 3 配置mybatis日志`src/main/resources/mybatis-config.xml`

```xml
<configuration>
    <settings>
        <setting name="logImpl" value="LOG4J"/>
    </settings>
    <environments default="development">
        <!-- ... -->
    </environments>
    <mappers>
        <!-- ... -->
    </mappers>
</configuration>
```



### 4 看下效果

```
- ==>  Preparing: SELECT id, user_name AS userName, age, sex, birth, mobile, create_user_id AS createUserId, create_time AS createTime, update_user_id AS updateUserId, update_time AS updateTime FROM t_user
- ==> Parameters: 
- <==      Total: 4
{1=User{id=1, userName='张三', age=12, sex=1, birth=Thu Oct 15 00:00:00 CST 2020, mobile='13111111111', createUserId=0, createTime=Thu Oct 15 00:00:00 CST 2020, updateUserId=0, updateTime=Thu Oct 15 00:00:00 CST 2020}, 2=User{id=2, userName='李四', age=13, sex=0, birth=Wed Oct 14 00:00:00 CST 2020, mobile='13111111113', createUserId=0, createTime=Thu Oct 15 00:00:00 CST 2020, updateUserId=0, updateTime=Thu Oct 15 00:00:00 CST 2020}, 3=User{id=3, userName='王五', age=14, sex=1, birth=Tue Oct 13 00:00:00 CST 2020, mobile='13111111112', createUserId=0, createTime=Thu Oct 15 00:00:00 CST 2020, updateUserId=0, updateTime=Thu Oct 15 00:00:00 CST 2020}, 4=User{id=4, userName='赵六', age=15, sex=1, birth=Mon Oct 12 00:00:00 CST 2020, mobile='13111111116', createUserId=0, createTime=Thu Oct 15 00:00:00 CST 2020, updateUserId=0, updateTime=Thu Oct 15 00:00:00 CST 2020}}

```

