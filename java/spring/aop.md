# AOP
>  AOP(Aspect Oriented Programming) 是对 OOP(Object Oriented Programming) 的补充，可以通过预编译的方式和运行期动态代理实现在不修改源代码的情况下给程序动态添加功能的一种技术，实现了调用者和被调用者之间的解耦。一些如日志、事务往往横切于业务代码中，但这些代码往往是重复的，AOP 就实现了把这些业务需求和系统需求分开来做。

Spring 中 AOP 代理由 Spring 的 IOC 容器负责生成、管理，其依赖关系也由 IOC 容器负责管理。

## Aspect
> Aspect（切面）：抽象的概念，表示整个切面逻辑，由 Advice 和 Pointcut 组成。

## Pointcut
> 是一个基于正则的 Aspect 表达式，是对执行点的定义，是程序执行点（JoinPoint）的集合。execution 是最常用的切点函数，切入点是方法。

`execution(<修饰符模式>? <返回类型模式> <方法名模式>(<参数模式>) <异常模式>?)` ：除了返回类型模式、方法名模式和参数模式外，其它项都是可选的。

| 通配符 | 匹配                                                         |
| :----: | :----------------------------------------------------------- |
|   *    | 匹配任意类型                                                 |
|   ..   | 用在包上，匹配当前包及其子包；<br />用在参数上，匹配任意参数 |
|   ()   | 匹配无参方法                                                 |
|  (..)  | 匹配任意方法                                                 |
|  (*)   | 匹配一个参数的方法                                           |

- execution(* io.jerry..*.*(..))：匹配 io.jerry 包及其子包下任意类的任意方法；
- execution(public * io.jerry.\*.\*(..))：匹配 io.jerry 包下任意类的任意 public 方法；
- execution(public * io.jerry..\*.\*(..))：匹配 io.jerry 包及其子包下任意类的任意 public 方法；
- execution(* io.jerry..UserService.*(*))：匹配 io.jerry 包及其子包下 UserService 接口的任意只有一个参数的方法；
- execution(* io.jerry..UserService+.*())：匹配 io.jerry 包及其子包下 UserService 接口的及其子类的任意无参方法。

## JoinPoint
> 切点/连接点，被拦截到的点，Pointcut 集合中具体的某个执行点。

!> Spring  只支持方法类型的连接点，所以这个连接点也就是被拦截到的方法。

## Advice
> 拦截到连接点之后要执行的代码，有 5 种类型。

?> 每种类型都可以传入 JoinPoint 参数来获取 target 对象的相关信息，但是只有 @Around 才能传入`ProceedingJoinPoint`参数。

| Advie          | 作用                                                         |
| -------------- | ------------------------------------------------------------ |
| @Around        | 环绕通知，类似 Filter 的 doFilter() 和 Interceptor 的 intercept() 方法，第一个参数 ProceedingJoinPoint （JoinPoint 的子类，添加了 proceed() 等方法） |
| @Before        | target 方法执行前调用                                        |
| @AfterReturn   | target 方法返回后调用（不包括抛出异常的情况）                |
| @After         | target 方法退出时调用（无论正常执行还是抛出异常）            |
| @AfterThrowing | target 方法抛出异常时调用                                    |

## Target
> 被 AspectJ 横切的对象.

```java
@Component
@Aspect
@Order(4)
//可以使用@Order注解指定切面的优先级，值越小优先级越高
public class ExceptionAspect{

	@Pointcut("execution(* io.jerry..*.*(..))")
	public void aspectJMethod() {};

	@Before("aspectJMethod()")
	public void printStartLog(JoinPoint joinPoint) {
		System.out.println(joinPoint.toLongString()); //通过JoinPoint对象获取target对象要执行的方法。
		System.out.println("Aop..Start.."+System.currentTimeMillis());
	}
	
	@AfterReturning("aspectJMethod()")
	public void printEndLog() {
		System.out.println("Aop..End.."+System.currentTimeMillis());
	}
	
	@Around("aspectJMethod()")
	public void printLog(ProceedingJoinPoint joinPoint) throws Throwable {
		System.out.println("LogAop..printArroundStartLog.."+System.currentTimeMillis());
		joinPoint.proceed(); //执行target方法。
		System.out.println("LogAop..printArroundEndLog.."+System.currentTimeMillis());
	}
}
```
