## Hibernate Session

Hibernate 的 session （非 web 层的`HttpSession`）相当于封装了一个 JDBC-Connection 的对象，是应用程序和数据库之间的一个会话。

> session 对象不是线程安全的，应该避免多个线程共享同一个 session 实例；

### 生命周期

session 的生命周期以事务的开始和结束为边界，生命周期绑定在了 transcation 对象上；

#### hibernate

Hibernate 本身是不具备事务处理能力的，Hibernate 的 Transaction 实际上是底层的 JDBC Transaction（默认）或 JTA Transaction 的封装。

```java
Session session=sessionFactory.openSession(); //new Connection()
Trancaction tx=session.beginTransaction();//conn.setAutoCommit(false);
session.save(user);
tx.commit();//conn.commit();
session.close();//conn.close();
```

所以Hibernate的Transcation很简单而已，就是调用了`conn.setAutoCommit(false)`而已。

> autoCommit=true（默认）：除非显式地开始一个事务，否则每个查询都被当做一个单独的事务自动执行；
>
> autoCommit=false ：用户将一直处于某个事务中，直到执行一条 commit 提交或 rollback 回滚语句才会结束当前事务并开启一个新的事务。

### Session缓存

session 对象内部有一个缓存，被称为 Hibernate 的第一缓存，每个 session 实例都有自己的缓存。session 加载对象后会为对象值类型的属性复制一份`快照`，当 session 清理缓存时，比较当前对象和它的快照就能知道哪些属性发生了变化，从而进行数据库的同步。

> 缓存用来减少数据库的频繁访问，缓存中的对象称为持久化对象；当调用 commit()，flush()，close() （内部调用 flush 方法 ）时会清理缓存，保证查询结果能反映对象的最新状态。

- ​

  - ​

    > 上下文可以设定 Thread（事务在单个数据库）或者 JTA（分布式，事务横跨多个数据库），需要在`SessionFactory`中配置`<property name="hibernate.current_session_context_class">thread/jta</property>`指定上下文的类型；

  - openSession() 得到的都是新创建的 session ，需要手动close()。

- session 对象使用完需要及时 close()，因为session对象中不仅保持着一个connection的连接，而且还保存着一些数据的缓存，close操作用于释放内存空间，将持久太对象变为托管态，防止不经意间修改数据库，但是不一定关闭了connection的连接，如果是从连接池拿到 connection，就是通知连接池回收该connection供其他请求使用。



## Hibernate SessionFactory

- 用来创建和管理 session 对象，是产生 session 对象（getCurrenSession/openSession方法）的工厂；
- 除非需要访问多个数据库，否则每个应用程序中只需要一个 sessionFactory；
- 因为产生 session 对象的代码都在 sessionFactory 对象的方法里，所以 sessionFactory 是线程安全的；
- 因为每个 session 对象中都有一个数据库的连接，所以产生 session 的工厂里边一定有很多的数据库连接，也就是 sessionFctory 里维护着一个数据库连接池，当产生 session 对象时它会从连接池里拿出一个`connection`交给 seesion 对象，当 session.close() 时就会清空 session 的缓存（Hibernate一级缓存）并把`connection` 连接返还给 sessionFactory。

### getCurrenSession()

获取当前线程里的 session，如果没有创建一个新的，得到的 session 对象会绑定到上下文中。在 `commit/rollback` 操作前，同一个上下文中再次调用该方法得到的还是同一个 session 对象。不能手动session.close()（`commit/rollback` 时会自动 close）。

> 例如在一个 userService 方法中调用了多个 xxDao 的方法，这时候 xxDao 方法中只能使用 getCurrentSession() ，否则每个 xxDao 得到的都是不同的 session ，这样就没办法进行统一的事务管理。

### openSession()

每次获得的是一个全新的 session 对象，需要手动 session.close();