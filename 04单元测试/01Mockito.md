# Mockito

<br>

```xml
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.powermock</groupId>
            <artifactId>powermock-api-mockito2</artifactId>
            <version>2.0.9</version>
            <scope>test</scope>
        </dependency>
        
        <dependency>
            <groupId>org.powermock</groupId>
            <artifactId>powermock-module-junit4</artifactId>
            <version>2.0.9</version>
            <scope>test</scope>
        </dependency>
```

<br>

`MockitoTest`

```java
public class MockitoTest {

    /**
     * 验证交互
     */
    @Test
    public void test1() {
        // 模拟创建
        List<String> mockedList = Mockito.mock(List.class);

        // 使用模拟对象 - 它不会抛出任何"意外互动"的异常。
        mockedList.add("one");
        mockedList.clear();

        // 选择性的、明确的。高度可读的验证
        verify(mockedList).add("one");
        verify(mockedList).clear();
    }

    /**
     * 存根方法调用
     */
    @Test
    public void test2() {
        // 可以模拟具体的类(LinkedList),而不仅仅是接口
        LinkedList<String> mockedList = Mockito.mock(LinkedList.class);

        // 打印null
        System.out.println(mockedList.get(0));

        // 存根出现在实际执行之前 (后面出现 mockedList.get(0) 其返回值为 "first")
        when(mockedList.get(0)).thenReturn("first");

        // 打印 first
        System.out.println(mockedList.get(0));
        System.out.println(mockedList.get(1));

        // 打印null
        System.out.println(mockedList.get(999));

    }

}
```





<br>

`PowerMockitoTest`

```java
@RunWith(PowerMockRunner.class)
@PrepareForTest({App.class})
public class PowerMockitoTest {

    @Test
    public void test3() {
        PowerMockito.mockStatic(App.class);

        PowerMockito.when(App.get(1)).thenReturn(50);
        int i = App.get(1);
        System.out.println(i);

        App app = PowerMockito.mock(App.class);
        PowerMockito.when(app.a()).thenReturn(10);
        int ret = app.a();
        Assert.assertEquals(10, ret);

    }
}
```



<br>

`PowerMock`集成`Spring`

```java
@RunWith(PowerMockRunner.class)
@PrepareForTest()
@PowerMockRunnerDelegate(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = {"classpath*:ctx.xml"})
@PowerMockIgnore({"javax.net.ssl.*", "javax.management.*", "javax.security.*", "javax.crypto.*"})
```

