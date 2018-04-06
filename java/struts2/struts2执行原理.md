# Struts2执行原理

> struts2 的核心原理就是通过拦截器来处理客户端的请求，经过拦截器一系列的处理后，再交给 Action 的指定方法处理。

![Struts2原理](http://wx1.sinaimg.cn/mw690/9e6aadb3gy1ffn657ssfkj20dm0czgmi.jpg)

1. 客户端发送 `HttpServletRequest` 请求，传递给 `Struts2PrepareAndExcuteFilter `（ActionMapper 是访问静态资源 【struts2 的 jar 文件等】时用的）；
2. 加载 web.xml 文件读取过滤器信息，经过一系列的过滤器之后，最后会经过 `Struts2PrepareAndExcuteFilter ` （简称 SPAE）过滤器；
3. SPAE 询问 `ActionMapper` 来决定请求是否要交给某个 `Action`(Controller)，如果是 SPAE 就把请求委托给 `ActionProxy`；
4. ActionProxy 对象通过 `ConfigurationManager` 对象获得 `struts.xml` 配置文件的内容，最后找到需要调用的目标 Action ，然后 ActionProxy 组建出 `ActionInvocation` 对象（包含一系列拦截器及 Action 执行链）；
5. ActionProxy 通过调用 ActionInvocation.invoke() 方法，来按顺序交由 `Interceptor` 处理，最后交由 Action，之后返回 Result，再逆序经过Interceptor，最后得到 HttpServletResponse 返回给客户端。

![Struts2原理时序图](http://wx1.sinaimg.cn/mw690/9e6aadb3gy1ffn69ozarwj20u50fqq3t.jpg)

1. 模拟 ActionInvocation：

```java
    package com.tgb.struts;  
    import java.util.ArrayList;  
    import java.util.List;  
    // 这个自定义的ActionInvocation是将Interceptor写在里面的，真实的ActionInvocation是通过反射加载的
    public class ActionInvocation {  
        List<Interceptor> interceptors = new ArrayList<Interceptor>();  
        int index = -1;  
        Action a = new Action();  

        public ActionInvocation() {  
            this.interceptors.add(new FirstInterceptor());  
            this.interceptors.add(new SecondInterceptor());       
        }  

        public void invoke() {  
            index ++;  
            if(index >= this.interceptors.size()) {  
                a.execute();  
            }else {  
                this.interceptors.get(index).intercept(this);  
            }  
        }  
    } 
```

2. 模拟 Interceptor 接口以及两个简单实现类：

```java
    package com.tgb.struts;  

    public interface Interceptor {  
        public void intercept(ActionInvocation invocation) ;  
    }  

    package com.tgb.struts;  

    public class FirstInterceptor implements Interceptor {  

        public void intercept(ActionInvocation invocation) {  
            System.out.println("FirstInterceptor Begin...");  
            invocation.invoke();  
            System.out.println("FirstInterceptor End...");  
        }  

    }  

    package com.tgb.struts;  

    public class SecondInterceptor implements Interceptor {  

        public void intercept(ActionInvocation invocation) {  
            System.out.println("SecondInterceptor Begin...");  
            invocation.invoke();  
            System.out.println("SecondInterceptor End...");  
        }  

    } 
```

3. Action：

```java
    package com.tgb.struts;  

    public class Action {  
        public void execute() {  
            System.out.println("Action Run...");  
        }  
    }
```

4. 测试：

```java
   package com.tgb.struts;  

    public class Client {  
        public static void main(String[] args) {  
            new ActionInvocation().invoke();  
        }  
    } 
```

 执行结果：

```java
    FirstInterceptor Begin...  
    SecondInterceptor Begin...  
    Action Run...  
    SecondInterceptor End...  
    FirstInterceptor End...  
```

通过上面的执行结果，可以看出客户端请求来的时候会按照顺序被所有配置的拦截器拦截一遍，然后返回的时候会按照逆序再被拦截器拦截一遍，这跟栈结构比较相似（先进先出）。