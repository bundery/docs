# Mapper映射文件

## resultMap 与 resultType

> 都是用来表示结果集与 javaBean 之间的映射关系，用于让 mybatis 把结果集设置在 javaBean 的属性中。

resultMap：配置 Java 对象属性与查询结果集中列名对应关系（不是数据库中的字段名）；手动配置映射关系，可以配置 typeHandler 进行数据类型的转换。

resultType：自动配置映射关系，放入自定义类型（大小写不敏感，要保证结果集中的列名与javaBean的属性名相同），或者 Map 中。

### ${} 与 #{}

${} 没有预编译，值会原样输出，有可能会导致 SQL 注入，但在某些场景下必须使用，如 `order by` 后面动态传入的列名； #{} 表示占位符`?`，属于预编译，相当于 JDBC 中的 preparedStatement ，可以防止 SQL 注入。

```java
//${}
String input = "id";
String sql = "select * from student order by "+input;
PreparedStatement st = con.prepareStatement(sql);
输出结果：select * from student order by id
SQL语句合法，正常执行。
-------------------------------------------------------------------------------------------
//#{}
String input = "id";
String sql = "select * from student order by ?";
PreparedStatement st = con.prepareStatement(sql);
st.setString(1,input);
输出结果：select * from student order by 'id'
order by 后面如果是采用预编译的形式动态输入参数，那么实际插入的参数是一个字符串，SQL语句不合法。
```









