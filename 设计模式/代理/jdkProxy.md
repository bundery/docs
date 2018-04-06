# JDK代理

JDK 动态代理通过反射类 Proxy 和 InvocationHandler 回调接口实现，相比静态代理最大的好处是接口声明的所有方法都被移到了 invoke 方法里，再也不怕接口方法多或接口方法变更了。

## 实现步骤

1. 自定义 InvocationHandler，在 invoke 方法中自定义拦截代码
2. 利用 Proxy 类生成代理对象，Object proxy=Proxy.newInstance(classLoader,interfaces,invocationHandler)
   - 根据参数 classLoader 和 interfaces 创建代理类，实现了 interfaces 接口，并继承 Proxy 类
   - 传入 invocationHandler 创建出代理对象

## 代理的方法

生成的代理类是 Proxy 的子类，代理了 `接口中的所有方法`  和 Proxy类的 `toString()`   、 `hashCode()`  、 `equals` 方法，不能代理 **private**、**final**、**static** 方法。

- 私有方法：私有方法不能被外界访问，内部调用的也不是代理后的私有方法，代理无意义；
- final 方法：可以代理 final 方法；但因为子类不能出现父类同名的 final 方法，所以只代理了 Proxy 类的 toString()、hashCode()、equals() 非 fianl 方法（继承自 Object 类）；
- 静态方法：根据 jdk 代理特点，调用的所有方法都是通过` super.h.invoke(this, m0, null)` 来完成的，静态方法内是不能直接调用非静态方法的。

## 特点

- 代理对象的生成要求委托类必须要`实现接口`；
- 如果需要调用委托类的的方法，就需要传入委托对象；
- **内部调用的方法**不会被代理，因为最终调用的是委托对象的原方法。

![jdk](http://wx4.sinaimg.cn/large/9e6aadb3gy1fmy6oxu8wnj20lr0d9dhh.jpg)

```java
//创建InvocationHandler
public class MyInvocationHandler implements InvocationHandler {

	private Object target; //委托类可以是任何类型

	public MyInvocationHandler(Object target) {
		this.target=target; //invocationHandler对象要包含委托对象，因为要调用委托对象的方法
	}

    //proxy:代理对象，method：委托类的方法，args：方法的参数
	@Override
	public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
		System.out.println("开始执行时间："+System.currentTimeMillis());
		Object result = method.invoke(target, args); //执行委托类的方法
		System.out.println("结束执行时间："+System.currentTimeMillis());
		return result;
	}
}


public class Client {
	@Test
	public void testAddUser() {
		UserService userService=new UserServiceImpl();
		InvocationHandler h=new MyInvocationHandler(userService);
		UserService proxy = (UserService)(Proxy.newProxyInstance(userService.getClass().getClassLoader(), userService.getClass().getInterfaces(), h));
		proxy.add(); 
	}
}


//Proxy类
class Proxy{  
    InvocationHandler h=null;  
    protected Proxy(InvocationHandler h) {  
        this.h = h;  
    }  
    ...  
}


//运行期间动态生成的代理类
public final class $Proxy0 extends Proxy implements Subject {  
    private static Method m0;  
    private static Method m1;  
    private static Method m2;  
    private static Method m3;  
    private static Method m4;  
 
    static {  
        try {  
            //object.hashCode()方法
            m0 = Class.forName("java.lang.Object").getMethod("hashCode" , new Class[0]);
            //object.equals()方法
            m1 = Class.forName("java.lang.Object").getMethod("equals" , new Class[] { Class.forName("java.lang.Object") });
            //object.toString()方法
            m2 = Class.forName("java.lang.Object").getMethod("toString", new Class[0]);
            //userService.add()方法
            m3 = Class.forName("*.UserService").getMethod("add", new Class[0]);
            //userService.delete()方法
            m4 = Class.forName("*.UserService").getMethod("delete", new Class[0]);
        } catch (NoSuchMethodException nosuchmethodexception) {  
            throw new NoSuchMethodError(nosuchmethodexception.getMessage());  
        } catch (ClassNotFoundException classnotfoundexception) {  
            throw new NoClassDefFoundError(classnotfoundexception.getMessage());  
        }  
    }
  
    public $Proxy0(InvocationHandler invocationHandler) {  
        super(invocationHandler);  //传入invocationHandler对象
    }  
  
    @Override  
    public final int hashCode() {  
        try {  
          	//调用 invaocationHandler 对象的 invoke() 方法
            return ((Integer) super.h.invoke(this, m0, null)).intValue();
        } catch (Throwable throwable) {  
            throw new UndeclaredThrowableException(throwable);  
        }  
    }  

    @Override  
    public final boolean equals(Object obj) {  
        try {  
            return ((Boolean) super.h.invoke(this, m1, new Object[] { obj })) .booleanValue(); 
        } catch (Throwable throwable) {  
            throw new UndeclaredThrowableException(throwable);  
        }  
    }  

    @Override  
    public final String toString() {  
        try {  
            return (String) super.h.invoke(this, m2, null); 
        } catch (Throwable throwable) {  
            throw new UndeclaredThrowableException(throwable);  
        }  
    }  
   
    public final void add() {  
        try { 
          	//调用代理对象的任何方法都会转到invocationHandler.invoke()
            super.h.invoke(this, m3, null);  
            return;  
        } catch (Error e) {  
        } catch (Throwable throwable) {  
            throw new UndeclaredThrowableException(throwable);  
        }  
    }  

    public final void delete() {  
        try {  
            super.h.invoke(this, m4, null);  
            return;  
        } catch (Error e) {  
        } catch (Throwable throwable) {  
            throw new UndeclaredThrowableException(throwable);  
        }  
    }  
} 
```

