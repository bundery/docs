# spring注解

使用注解之前，要先在 application.xml 文件中添加`<context:component-scan base-package="com.jerry">`，配置这个标签之后，Spring 会初始化默认的 4 个相关的 bean（用于处理相关注解），在容器启动时扫描指定包下带 @Component 等注解的类，并初始化为 bean 交由spring IOC 管理。

## @Component
@Component("bean_id")/@Controller("...")/@Service("...")/@Repository("...") 注解在类上，可以把此类作为一个 bean 交给 IOC 容器管理。

## @Scope
@Scope("singleton")/@Scope("prototype")，注解在类上，用于指定 bean 的生命周期。

## @Required
注解在类的 setter 方法上，要求必须注入相关属性，类似于`@Override`的编译时检查。

## @AutoWired
可以注解在类的 setter 方法上，然后注入指定的属性，默认按 ***类型*** 注入，如果容器中找到不止一个匹配的 bean，则抛出异常。如果要按名称装配，可以配合`@Qualifier`使用，如果允许要注入的属性值为`null`，则可以使用`@AutoWired(required="false")`。

```java
@Autowired
public void setUserDao(@Qualifier("userDao") UserDao userDao){
    this.userDao=userDao;
}
```

## @Resource
可以注解在类的 setter 方法上，然后注入指定的属性。

* 如果不指定 name 和 type，即 @Resource()，则按 ***名称*** 装配，找不到按类型装配。
* 如果只指定 name，即 @Resource(name="...")，则按名称装配，根据 name 在 IOC 容器中找匹配的 bean，找不到抛出异常。
* 如果只指定 type，即 @Resource(type="...")，则按类型装配，找不到或者找到多个，则抛出异常。

## @PostConstruct
注解于方法上，相当于 init-method。

## @PreDestory
注解于方法上相当于 destory-method。

## @Transcational
注解于类或者方法上，用于声明式事务管理。需要在配置文件中添加`<tx:annotation-driven transaction-manager="transactionManager" />`，该配置就是把所有已加载进 IOC 容器中带有 `@Transcational` 注解的 bean 交给指定的事务管理器进行事务管理。

> 注解在类上，会对所有的 public 方法起作用；注解在方法上会覆盖掉类上的注解。