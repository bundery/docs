# CGLIB代理

使用 cglib(Code Generation Library) 实现动态代理，不要求委托类必须实现接口，底层采用 asm 框架生成代理类的字节码，需要导入第三方 jar 包。

## 实现步骤

1. 实现 MethodInterceptor，定义方法的拦截器。
2. 利用 Enhancer 类生成代理类对象。
    1. 生成委托类的子类的二进制字节码。
    2. 通过 Class.forName 加载二进制字节码到内存，生成 *.Class 对象。
    3. 生成并初始化代理类对象。

## 代理的方法

不能代理`private`、`final`、`static`方法。

- 私有方法：私有方法不能被外界访问，内部调用的也不是代理类的方法，还是委托类的方法，因为私有方法属于`静态绑定` ，代理也无意义；
- final 方法：cglib 代理类继承的委托类，子类不能出现父类同名的 final 方法，无法被代理；
- 静态方法： 静态方法不用创建代理类就能直接访问，那么`myMethodInterceptor.interceptor(...)` 无法直接访问。

## 特点

1. 不需要实现接口，代理对象是委托对象的子类；
2. 不需要传入委托对象，因为子类/代理类可以访问父类/委托类的方法；
3. 内部调用的非`私有、final、静态方法`也会被代理。

![cglib](http://wx1.sinaimg.cn/large/9e6aadb3gy1fmy68ac5wfj20m10dxjt3.jpg)

```java
//MethodInterceptor
public class MyMethodInterceptor implements MethodInterceptor {
    //proxy: 代理对象 method:委托类中委托的方法对象 args:参数 methodProxy:代理方法的MethodProxy对象
	@Override
	public Object intercept(Object proxy, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
		System.out.println("开始运行时间："+System.currentTimeMillis());
		Object result = methodProxy.invokeSuper(proxy, args);//每个被代理的方法都对应一个MethodProxy对象，最终调用委托类的原方法
		System.out.println("结束运行时间："+System.currentTimeMillis());
		return result;
	}
}

//生成代理对象
public class Client {
	@Test
	public void testAddUser() {
		Enhancer enhancer=new Enhancer();
		enhancer.setSuperclass(UserServiceImpl.class);
		enhancer.setCallback(new MyMethodInterceptor());
		UserServiceImpl proxy=(UserServiceImpl)(enhancer.create());
		proxy.add();
	}
}
```

`enhancer.create();` 执行后生成了**委托类的子类**的二进制代码，每个委托方法都生成了相应的俩个方法，以`add()` 方法来讲，生成了一个重写的`add()`方法（内部调用 methodInterceptor.intercept() ），另外一个是`CGLIB$add$0()`方法（通过 super 指针调用原方法）。


```java
//生成的代理类伪代码
public class UserServiceImpl$CGLIB extends UserServiceImpl implements Factory
{
    ...otherCode...
    /**
    *覆盖委托类中的add方法
    * this:代理对象
    * Method:委托类的方法对象
    * MethodProxy:封装了CGLIB$add$0()方法的对象
    */
    public final void add() {
            myMethodInterceptor.intercept(this, Method, args, MethodProxy);
            return;
    }
    //同时生成的另外一个方法（MethodProxy.invokeSuper()就是调用的这个方法）
    final void CGLIB$add$0() {
        super.add();
    }
    ...otherCode...
}
```