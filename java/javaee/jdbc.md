# JDBC

## DataSource

是一个提供标准化的取得数据库连接方式的接口，该接口有一个 getConnection() 方法，用以取得数据库连接，实现一个数据库连接池统一实现此接口。

## Connection Pool
由于数据库连接的创建和销毁非常消耗系统资源，因此可以提前创建多个 connection 放在一个容器里，使用时直接从容器中取，使用完再放回池里供下个请求使用。从连接池中获取到的连接，通常已经被扩展了，当调用 connection.close() 方法时，实际上并不会关闭连接，而是通知连接池这个连接已经又可以使用了。

一个标准的数据库连接池至少需要：
* 实现 DataSource 接口
* 有一个容器可以管理多个连接
* 必须拥有回收连接的能力
* 数据库用户名及连接密码
* ConnectionURL（如：`jdbc:mysql://localhost:3306/helloWorld`）
* 相应的数据库驱动（如：`com.mysql.jdbc.Drive`）
* MaxPoolSize 连接池中最大连接数
* MinPoolSize 连接池中最小连接数
* ...

