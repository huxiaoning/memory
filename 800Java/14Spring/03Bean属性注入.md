# Bean属性注入

- 有参构造函数注入
- set方法注入



### set方法注入

```xml
    <bean id="people" class="org.example.pojo.People">
        <property name="id" value="1"/>
        <property name="name" value="张三"/>
    </bean>
```

或者

```xml
    <bean id="people" class="org.example.pojo.People">
        <property name="id">
            <value>1</value>
        </property>
        <property name="name">
            <value>张三</value>
        </property>
    </bean>
```



```java
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        People people = context.getBean("people", People.class);
        System.out.println(people);
```



### 集合类型的属性注入

```java
import java.util.Arrays;
import java.util.List;
import java.util.Map;
import java.util.Set;
public class People {
    private Integer id;
    private String  name;
    private People friend;
    private Set<String> sets;
    private List<String> lists;
    private String[] arrs;
    private Map<String, Object> maps;
    private Properties properties;
	// ignore get set toString
}
```

```xml
<bean id="people" class="org.example.pojo.People">
        <property name="id">
            <value>1</value>
        </property>
        <property name="name">
            <value>张三</value>
        </property>
        <property name="friend" ref="friend"/>
        <property name="sets">
            <set>
                <value>1</value>
                <value>2</value>
            </set>
        </property>
        <property name="lists">
            <set>
                <value>3</value>
                <value>4</value>
            </set>
        </property>
        <property name="arrs">
            <array>
                <value>5</value>
                <value>6</value>
            </array>
        </property>
        <property name="maps">
            <map>
                <entry key="a">
                    <value>1</value>
                </entry>
                <entry key="b">
                    <value>1</value>
                </entry>
            </map>
        </property>
        <property name="properties">
            <props>
                <prop key="xxx">aaa</prop>
                <prop key="yyy">bbb</prop>
            </props>
        </property>
    </bean>
```

其中

value属性可以用ref属性替换。

value标签可以用ref标签替换。

- value
  - 赋值基本类型
- ref
  - 赋值已定义的bean引用。

