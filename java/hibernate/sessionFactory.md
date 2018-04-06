# SessionFactory

用来创建和管理 session 对象，是产生 session 对象（getCurrenSession/openSession方法）的工厂。因为每个 session 对象中都有一个数据库的连接，所以产生 session 的工厂里边一定有很多的数据库连接，也就是 sessionFctory 里维护着一个数据库连接池，当产生 session 对象时它会从连接池里拿出一个`connection`交给 seesion 对象，当 session.close() 时就会清空 session 的缓存（Hibernate一级缓存）并把 connection 连接返还给 sessionFactory。

> 1. 除非需要访问多个数据库，否则每个应用程序中只需要一个 sessionFactory；
> 2. 创建 session 对象的代码都在 sessionFactory 对象的方法里，所以 sessionFactory 是线程安全的。

## getCurrenSession

获取当前线程里的 session，如果没有创建一个新的，得到的 session 对象会绑定到上下文中。在 `commit/rollback` 操作前，同一个上下文中再次调用该方法得到的还是同一个 session 对象。不能手动 session.close()（ 因为commit/rollback 时会自动 close）。

> 例如在一个 userService 方法中调用了多个 xxDao 的方法，这时候 xxDao 方法中只能使用 getCurrentSession() ，否则每个 xxDao 得到的都是不同的 session ，这样就没办法进行统一的事务管理。

## openSession

每次获得的是一个全新的 session 对象，需要手动 session.close();