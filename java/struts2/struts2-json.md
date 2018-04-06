# Struts2-json

1. 导入`struts2-json-plugin`包，它是 Struts2 的 json插件，它可以把对象中有 getter 方法的属性全部被转换为 json 字符串。
2. 在 struts2.xml 中，package 继承`json-default`，Action 的返回结果集内设置成`result type="json"`。

```xml
<package name="myJson" extends="json-default">
    <action name="user_*" class="com.bundery.tax.user.action.UserAction">
    <!--因为返回的是json数据，并不是页面，所以result内不能配置返回页面-->
        <result name="success" type="json">
            <!--把userAction对象返回-->
            <param name="root">action</param>
        </result>
    </action> 
</package>
```

此插件默认解析 ValueStack 栈顶对象。

1. 如果 UserAction 没有实现 ModelDriven 接口，默认会把 UserAction 的所有 getter 方法返回的属性解析为字符串。
    - 如果不想让某个 getter 返回，就在 getter 方法上添加`@json(serialize=false)`。
    - 或者在配置文件中，设置包含属性。`<param name="includeProperties">products\[\d+\]\.name</param>`(设置只包含name属性)
2. 如果 UserAction 实现了 ModelDriven 接口，默认会把 model 对象转换成 json 格式返回，可以通过`<param name="root">action</param>`来设置让 UserAction 对象转换为 json 格式返回。
