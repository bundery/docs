# Junit

## RunWith

JUnit用例都是在 Runner（运行器）来执行的。通过它，可以为这个测试类指定一个特定的 Runner。如果不指定 会使用默认的 Runner， 是 BlockJunit4ClassRunner（JUnit4.4之后），Junit4ClassRunner（Junit4.4 之前）。

- **@RunWith(Suite.class)**

  > Suite 类是 JUnit 自带的，意为套件，用来使 JUnit 执行一个测试套件，通过它可以把多个相关的测试类看做一个测试套件一起测试。

  ```java
  @RunWith(Suite.class)
  @Suite.SuiteClasses({ TestA.class, TestB.class})
  public class TestSuite {
      // 自身的就不会执行了，只会执行Test.A和Test.B测试类了
      @Test
      public void testPrint() {
          System.out.println("Hello");
      }
  }
  ```

- **@RunWith(SpringJUnit4ClassRunner.class)**

  > 由Spring提供，表明这个类中的测试用例需要使用 SpringJUnit4ClassRunner 类来执行

  ```java
  @RunWith(SpringJUnit4ClassRunner.class)
  @ContextConfiguration(locations = { "classpath:applicationContext.xml" })
  public class UserManagerTest {
  　　@Autowired
  　　ApplicationContext ctx;
  　　@Test
  　　public void testAddUser() {
  　　　　UserManager userManager = ctx.getBean(UserManager.class); 
  　　　　userManager.addUser();
    }
  }
  ```



## RULE

@Rule 注解是 Junit4 的新特性，只能注解在字段中，该字段必须是`public`且实现了`TestRule` （相当于拦截器）接口的。跟`@Before @After`实现同样的效果，每个测试方法执行时都会调用被注解的`Rule`，当多个类的方法都需要执行同样的逻辑时，使用 @Rule 代替 @Before 可以省略代码。

```java
//自定义Rule
public class LoggerRule implements TestRule {
    @Override
    public Statement apply(Statement base, Description description) {
        return new Statement() {
            @Override
            public void evaluate() throws Throwable {
                System.out.println("begin");
                base.evaluate();
                System.out.println("end");
            }
        };
    }
}

//使用自定义Rule
public class RuleTest {
    @Rule
    public TestRule loggerRule=new LoggerRule();
    @Test
    public void testMethod1() {
        System.out.println("testMethod1");
    }
    @Test
    public void testMethod2() {
        System.out.println("testMethod2");
    }
}
/*
打印结果：
begin
testMethod1
end
begin
testMethod2
end
*/
```

更多的情况时使用 Junit 内置的 Rule，如`Timeout`，`TemporaryFolder`

```java
//所有的测试方法运行时间都不能超过1秒钟，否则测试不通过
public class TimeoutTest {
    @Rule
    public Timeout timeout=new Timeout(1000);
    @Test
    public void testMethod1() {
        System.out.println("testMethod1");
    }
    @Test
    public void testMethod2() {
        System.out.println("testMethod2");
    }
}

//测试抛出的异常，加强版的@Test(expected=NullPointerException.class)
public class ExceptionTest {
    @Rule
    public ExpectedException expected = ExpectedException.none();
    @Test
    public void testMethod() {
        expected.expect(RuntimeException.class);
        //测试抛出的异常Message，测试通过
        expected.expectMessage("Runtime exception occurred");
        throw new RuntimeException("Runtime exception occurred");
    }
    @Test
    public void testMethod2() {
        expected.expect(RuntimeException.class);
        //测试抛出的异常Message，测试不通过
        expected.expectMessage("Runtime exception occurred");
        throw new RuntimeException("Runtime");
    }
}
```

> Junit 4.9 之后出现了 @ClassRule，注解在类上，跟`@BeforeClass @AfterClass` 实现同样的效果。