# Mybatis插件

> Mybatis 拦截器（Plugin）可以拦截某些方法的调用，在这些被拦截的方法执行前后添加自定义代码。Mybatis 拦截器设计的初衷是为了供用户在某些时候实现自己的代码而不用去修改 Mybatis 固有的代码。

## 自定义拦截器类

```java
//拦截Executor接口的update方法
@Intercepts({@Signature(type= Executor.class,method = "update",args = {MappedStatement.class,Object.class})})
public class ExamplePlugin implements Interceptor {
  public Object intercept(Invocation invocation) throws Throwable {
    //原始方法执行前的代码
    Object object = invocation.proceed();//执行原始方法
    //原始方法后执行的代码
    return object;
  }
  public Object plugin(Object target) {return Plugin.wrap(target, this); }
  public void setProperties(Properties properties) {}
}
```

在 mybatis-config.xml 配置文件中定义：

```xml
<plugins>
    <plugin interceptor="org.ExamplePlugin"></plugin>
</plugins>
```

## 原理

当调用 `SqlSessionFactoryBuilder.build(inputStream)` 方法构建 SqlSessionFactory 时，会先创建出configuration 对象，并把配置文件中 `<plugin>` 标签定义的所有拦截器设置到`interceptorChain`（configuration 的一个属性）容器中。

```java
public class InterceptorChain {
  private final List<Interceptor> interceptors = new ArrayList<Interceptor>();
  public Object pluginAll(Object target) {
    /*
    遍历所有定义的拦截器
    */
    for (Interceptor interceptor : interceptors) {
      target = interceptor.plugin(target);//调用Plugin.wrap(...)
    }
    return target;
  }
  public void addInterceptor(Interceptor interceptor) {
    interceptors.add(interceptor);
  }
  public List<Interceptor> getInterceptors() {
    return collections.unmodifiableList(interceptors);
  }
}


public class Plugin implements InvocationHandler {
  private final Object target;
  private final Interceptor interceptor;
  private final Map<Class<?>, Set<Method>> signatureMap;//注解表明要拦截的所有接口
	...
  /*
  判断target实现的接口是不是要拦截的接口，如果是就返回代理对象，否则返回原target对象
  */
  public static Object wrap(Object target, Interceptor interceptor) {
    Map<Class<?>, Set<Method>> signatureMap = getSignatureMap(interceptor);
    Class<?> type = target.getClass();
    Class<?>[] interfaces = getAllInterfaces(type, signatureMap);
    if (interfaces.length > 0) {//如果target实现的接口是要拦截的接口就创建代理类对象返回
      return Proxy.newProxyInstance(
          type.getClassLoader(),
          interfaces,
          new Plugin(target, interceptor, signatureMap));
    }
    return target;//返回原对象
  }

  /*
  判断执行的方法是不是要拦截的方法，如果是就执行intercept(...)方法，否则执行原对象的原方法
  */
  @Override
  public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
    try {
      Set<Method> methods = signatureMap.get(method.getDeclaringClass());
      if (methods != null && methods.contains(method)) {
        return interceptor.intercept(new Invocation(target, method, args));
      }
      return method.invoke(target, args);//调用原对象的原方法
    } catch (Exception e) {
      throw ExceptionUtil.unwrapThrowable(e);
    }
  }
	...
}


public class Invocation {
  private final Object target;
  private final Method method;
  private final Object[] args;

  public Invocation(Object target, Method method, Object[] args) {
    this.target = target;
    this.method = method;
    this.args = args;
  }

  public Object proceed() throws InvocationTargetException, IllegalAccessException {
    return method.invoke(target, args);//调用原方法
  }

}
```

默认情况下 Mybaitis 自定义的拦截器可以拦截 `Executor，ParameterHandler，ResultSetHandler，StatementHandler` 方法，是因为在新建它们对象时都调用了 interceptorChain.pluginAll(...)。

?> 在构建对象的过程中只要调用了 interceptorChain.pluginAll(...)，拦截器链都会起作用。

1. 创建 SqlSession 对象，会首先调用 configuration.newExecutor(...) 创建 Executor 对象；
2. 执行查询动作，执行 executor.doQuery(...)，内部调用 configuration.newStatementHandler(...)  创建 StatementHandler 对象；
3. StatementHandler 构造方法内部会依次调用  configuration.newParamterHandler(...) 及configuration.newResultSetHandler(...)。

![创建Handler](http://wx1.sinaimg.cn/large/9e6aadb3gy1flcgh14ttgj20mq0fe75i.jpg)

```java
public class Configuration{
    public ParameterHandler newParameterHandler(MappedStatement mappedStatement, Object parameterObject, BoundSql boundSql) {
    ParameterHandler parameterHandler = mappedStatement.getLang().createParameterHandler(mappedStatement, parameterObject, boundSql);
    parameterHandler = (ParameterHandler) interceptorChain.pluginAll(parameterHandler); //生成代理对象
    return parameterHandler;
  }

  public ResultSetHandler newResultSetHandler(Executor executor, MappedStatement mappedStatement, RowBounds rowBounds, ParameterHandler parameterHandler,
      ResultHandler resultHandler, BoundSql boundSql) {
    ResultSetHandler resultSetHandler = new DefaultResultSetHandler(executor, mappedStatement, parameterHandler, resultHandler, boundSql, rowBounds);
    resultSetHandler = (ResultSetHandler) interceptorChain.pluginAll(resultSetHandler);
    return resultSetHandler;
  }

  public StatementHandler newStatementHandler(Executor executor, MappedStatement mappedStatement, Object parameterObject, RowBounds rowBounds, ResultHandler resultHandler, BoundSql boundSql) {
    StatementHandler statementHandler = new RoutingStatementHandler(executor, mappedStatement, parameterObject, rowBounds, resultHandler, boundSql);
    statementHandler = (StatementHandler) interceptorChain.pluginAll(statementHandler);
    return statementHandler;
  }

  public Executor newExecutor(Transaction transaction) {
    return newExecutor(transaction, defaultExecutorType);
  }

  public Executor newExecutor(Transaction transaction, ExecutorType executorType) {
    executorType = executorType == null ? defaultExecutorType : executorType;
    executorType = executorType == null ? ExecutorType.SIMPLE : executorType;
    Executor executor;
    if (ExecutorType.BATCH == executorType) {
      executor = new BatchExecutor(this, transaction);
    } else if (ExecutorType.REUSE == executorType) {
      executor = new ReuseExecutor(this, transaction);
    } else {
      executor = new SimpleExecutor(this, transaction);
    }
    if (cacheEnabled) {
      executor = new CachingExecutor(executor);
    }
    executor = (Executor) interceptorChain.pluginAll(executor);
    return executor;
  }

}
```

## 执行流程图

![mybatis拦截器](https://wx4.sinaimg.cn/mw690/9e6aadb3gy1fkh2nvwqbrj20pr0iwaas.jpg)