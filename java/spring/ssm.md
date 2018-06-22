# SSM整合

MyBatis 的核心是`SqlSessionFactory`，Spring的核心是`BeanFactory`或`ApplicationContext`。把两者整合在一起，`Mybatis-Spring`封装了一个SqlSessionFactoryBean，在这个Bean里可以创建SqlSessionFactory。所以通过将SqlSessionFactoryBean对象注入注入即可将二者整合。

## DataSource

```xml
<!-- 引入JDBC文件-->
<context:property-placeholder location="classpath:jdbc.properties" />
<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
  <property name="driverClassName" value="${jdbc.driver}" />
  <property name="url" value="${jdbc.url}" />
  <property name="username" value="${jdbc.username}" />
  <property name="password" value="${jdbc.password}" />
</bean>
```

## SessionFactory

为整合应用提供SqlSession对象资源。单独使用Mybatis时，通过SqlSessionFactoryBuilder对象建造，跟Spring整合时，注册SqlSessionFactoryBean对象。

```xml
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
  <!-- 用于指定mybatis的数据源 -->
  <property name="dataSource" ref="dataSource" />
  <!-- 用于指定mybatis中mapper文件所在的位置 -->
  <property name="mapperLocations" value="classpath:mapper/*.xml" />
  <!-- 自动重命名 -->
  <property name="typeAliasesPackage" value="com.test.mybatis_spring.model" />
  <!-- 用于指定mybatis配置文件的位置 -->
  <property name="configLocation" value="classpath:mybatis-config.xml" />
</bean>
```

可选属性：

- mapperLocations：Mybatis映射器存在的位置，如果映射器跟Mapper接口在同一个路径下，该属性不用配置。
- configLocation：用于指定Mybatis的配置文件，如果指定了该属性，那么会以该配置文件的内容作为配置信息构建对应的SqlSessionFactoryBuilder，但是后续属性指定的内容会覆盖该配置文件里面指定的对应内容。
- typeAliasesPackage：用于给包中的类注册别名，注册后可以直接使用类名，而不用使用全限定的类名（就是不用包含包名），多个package之间可以用逗号或者分号等来进行分隔。

## MapperFactoryBean

根据Mapper接口获取代理后的Mapper对象，因为实现了FactoryBean接口，所以在IOC容器中存在的是getObject()返回的对象，而不是mapperFactoryBean对象本身，该对象需要注入Mapper接口类对象以及用于产生sqlSession的sqlSessionFactory对象（会自动注入）。

```java
//伪代码
public class MapperFactoryBean<T> extends SqlSessionDaoSupport implements FactoryBean<T> {
    private Class<T> mapperInterface;
    public MapperFactoryBean(Class<T> mapperInterface) {
        this.mapperInterface = mapperInterface;
    }
      public T getObject() throws Exception {
        return this.getSqlSession().getMapper(this.mapperInterface);
    }
}

```

在配置文件中配置：

```xml
<bean id="userMapper" class="org.mybatis.spring.mapper.MapperFactoryBean">
  <property name="mapperInterface" value="com.sample.mapper" />
  <property name="sqlSessionFactory" ref="sqlSessionFactory" />
</bean>
```

> 如果 UserMapper 有一个对应的 MyBatis 的 XML 映射器文件且所在位置和映射器类相同时, 它会被MapperFactoryBean自动解析，不用在 MyBatis 配置文件中去指定映射器 , 除非映射器的XML文件在不同的类路径下。

## MapperScannerConfigurer

如果有大量 MapperFactoryBean 需要注册，那配置 XML 文件就会很麻烦，因为每一个Mapper接口就要实现一个 MapperFactoryBean 对象，所以此对象用来根据指定的包批量扫描Mapper接口创建对应的 MapperFactoryBean 对象然后注册在IOC容器中。

```xml
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
  <property name="basePackage" value="com.sample.mapper" />
</bean>
```

属性：

- annotationClass：当指定了annotationClass时，MapperScannerConfigurer将只注册使用了annotationClass注解标记的接口。

## 事务

`MyBatis-Spring` 利用 Spring 提供的 `DataSourceTransactionManager` 进行事务管理。

```xml
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
  <property name="dataSource" ref="dataSource" />
</bean>
```

