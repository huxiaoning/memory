# Spring创建对象的三种方式

- 通过构造方法创建
  - 无参构造创建：默认情况
  - 有参构造创建：需要明确配置
- 实例工厂
- 静态工厂



### 有参构造

```xml
    <bean id="people" class="org.example.pojo.People">
        <constructor-arg name="id" value="1"/>
        <constructor-arg name="name" value="张三"/>
    </bean>
```

```java
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        People people = context.getBean(People.class);
        System.out.println(people);
```

##### `<constructor-arg/>`标签属性说明

- 指定初始化字段
  - name
    - `<constructor-arg name="id" value="1"/>`
  - index
    - 指定是第几个参数`<constructor-arg index="0" value="1"/>`,索引从0开始
  - type
    - `<constructor-arg type="int" value="1"/>`
    - `<constructor-arg type="java.lang.Integer" value="1"/>`
- 赋值属性
  - value
    - 配置常量值
  - ref
    - 配置id，引用bean



### 实例工厂

```xml
    <!-- 无参构造工厂 -->
    <bean id="peopleFactory" class="org.example.pojo.PeopleFactory"/>
    <!-- 实现工厂创建bean -->
    <bean id="peopleA" factory-bean="peopleFactory" factory-method="newPeople"/>
```

```java
public class PeopleA extends People {
}
public class PeopleFactory {
    public People newPeople() {
        return new PeopleA();
    }
}
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        People people = context.getBean(People.class);
        System.out.println(people);
    }
```



### 静态工厂（简单工厂）

```xml
    <!-- 静态工厂创建bean -->
    <bean id="peopleA" class="org.example.pojo.PeopleFactory" factory-method="newPeople"/>
```

```java
public class PeopleA extends People {
}
public class PeopleFactory {
    public static People newPeople() {
        return new PeopleA();
    }
}
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        People people = context.getBean(People.class);
        System.out.println(people);
    }
```

