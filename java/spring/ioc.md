# IOC/DI

 IOC(`Inversion of control`) 控制反转 / DI(`Dependency Injection`) 依赖注入。没有 IOC/DI 容器的时候，当 A 类需要使用 B 类对象就会主动创建 B 对象，而有了容器后 A 类就不再主动去创建 B 类对象了，而是从 IOC/DI 容器获取一个 B 类对象，然后注入到 A 类中。IOC/DI 容器在这个过程中负责创建 B 的实例（可能会提前创建好），然后收到注入请求后注入对象。

> IOC 及 DI 是对同一件事情的不用描述。依赖注入是从使用者的角度来描述的（使用者依赖容器注入所需要的外部资源），控制反转是从容器的角度来描述的（容器控制应用程序）。

## BeanFactory

BeanFactory 定义了 IOC 容器的最基本形式，是 IOC 容器应遵守的最基本最底层的接口，用于规范统一获取Bean对象的方法（`getBean方法`）。BeanFactory 只是一个接口，并不是 IOC 的具体实现，Spring 内置了多种实现，如 ApplicationContext（也是接口，实现类有 ClasspathXmlApplicationContext），该容器类可以读取 XML文件并初始化配置在文件中的 bean，以及管理他们的整个声明周期。

下面是 getBean(name) 方法的伪代码：

```java
public abstract class AbstractBeanFactory {
    protected Object getBean(Object beanInstance, String name) {

        //如果beanName以&开头但不是FactoryBean的实例就抛出异常
        if (name.startsWith("&") && !(beanInstance instanceof FactoryBean)) {
            throw new Exeception();
        }

        //如果不是FactoryBean的实例，或者是FactoryBean的实例但是beanName是以&开头就直接返回实例
        if (!(beanInstance instanceof FactoryBean) || name.startsWith("&")) {
            return beanInstance;
        }

        //如果是FactoryBean的实例且beanName不是以&开头就调用实例的getObject()获得对象返回
        FactoryBean<?> factory = (FactoryBean<?>) beanInstance;

        return factory.getObject();
    }
}
```

> Spring 中有两种类型的Bean，分别是普通 Bean 和工厂 Bean（FactoryBean），这两种Bean都被容器管理，但工厂 Bean 跟普通 Bean 不同，其返回的对象不是指定类的一个实例，其返回的是该 FactoryBean 的getObject 方法所返回的对象，相当于 factoryBean.getObject() 代理了 beanFactory.getBean(beanName) 方法。

### FactoryBean

FactoryBean 接口是 Spring IOC 容器的扩展，如果初始化代码非常复杂，此时在 XML 文件中需要写大量 `<property>`标签，而使用 JAVA 编码比使用 XML 配置更容易表达。这种场景中就可以自定义 FactoryBean，在类中撰写复杂的初始化程序，并将其作为 Bean 加入到容器中。这种方式隐藏了实例化一些复杂 Bean 的细节，给上层应用带来了便利。

如果直接配置下面的 Phone 类，则 Phone 的每个属性都要配置一个 \<property\> 标签：

```xml
<bean id="phone" class="com..PhoneFactoryBean" p:phone="苹果,IOS,黑色">	
	<property name="name" value="苹果" />
	<property name="system" value="IOS" />
	<property name="color" value="黑色" />
</bean>  
```

```java
public class Phone {
    private String name;
    private String system;
    private String color;
	//...setters&getters
}
```

而如果实现了 FactoryBean ，就可以只配置一个 phoneInfo 信息：

```xml
<bean id="phone" class="com..PhoneFactoryBean" p:phone="苹果,IOS,黑色"/>
```

```java
public class PhoneFactoryBean implements FactoryBean {
    private String phoneInfo;
    public void setPhoneInfo(String phoneInfo) {
        this.phoneInfo = phoneInfo;
    }
  
    @Override
    public Object getObject() throws Exception {
        Phone phone=new Phone();
      	//分割逗号，为对象设置属性
        String[] infos = phoneInfo.split(",");
        phone.setName(infos[0]);
        phone.setSystem(infos[1]);
        phone.setString(infos[2]);
        return phone;
    }
  
  	//由FactoryBean创建的对象的类型
    @Override
    public Class<?> getObjectType() {
        return Phone.class;
    }
  
  	//由FactoryBean创建的实例是不是单例的
    @Override
    public boolean isSingleton() {
        return false;
    }
}
```

## 获取容器对象

1. 手动初始化容器：

  ```java
  //这种方式创建容器时会重新创建配置文件中配置的bean
  ApplicationContext ac=new ClasspathXmlApplicationContext("classpath:applicationContext.xml");
  ```

2. 通过 Spring 提供的工具类获取ApplicationContext对象：

  ```java
  //如果applicationContext对象不存在，就抛出异常
  ApplicationContext ac1 = WebApplicationContextUtils.getRequiredWebApplicationContext(ServletContext sc);
  //如果applicationContext对象不存在，返回null
  ApplicationContext ac2 = WebApplicationContextUtils.getWebApplicationContext(ServletContext sc);
  ```

3. 自定义类实现  `ApplicationContextAware` 接口，并配置在 applicationContext.xml 文件中，Spring 初始化该类时，会自动把 applicationContext 对象注入。