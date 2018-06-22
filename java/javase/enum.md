# 枚举

java 1.5 之后出现了枚举（Enumeration），它用来将一组类似的值包含到一种类型当中，没有枚举之前，我们是这样做的：

```java
public class WeekDay{
    private final static int SUNDAY=0;
    private final static int MONDAY=1;
	...
}
```

使用的时候，直接在程序中引用这些常量，但是这种方式存在一些问题：

- 类型不安全： 星期的常量对应的值是整数型，所以使用过程中可以传入任意的整数值而没有限制，导致出现错误
- 对象无意义：由于对象的值只是一些整数，打印出来只是一些 开发者 才认识的数字

 所以我们可以使用枚举来代替这种做法：

```java
public enum WeekDay {
  	//都是WeekDay的实例
    SUN(0),MON(1),TUE(2),WED(3),THI(4),FRI(5),SAT(6);
    private int index;
    WeekDay(int index) {
        this.index = index;
    }
}

//反编译的结果，可以看到枚举类默认继承了java.lang.Enum，因此不能再继承其他类了，但可以实现接口
public class WeekDay extends Enum<WeekDay>{
    private int index;
    private WeekDay(int index){
        this.index=index;
    }
    private final static WeekDay SUNDAY=new WeekDay(0);
    private final static WeekDay MONDAY=new WeekDay(1);
	...
    //自己合成了存储枚举对象的数组
    private static final WeekDay[] $VALUES;
	static {
        $VALUES = new WeekDay[]{SUNDAY, MONDAY...};
    }
    public static WeekDay[] values() {
        return (WeekDay[])$VALUES.clone();
    }
    public static WeekDay valueOf(String string) {
        return Enum.valueOf(WeekDay.class, string);
    }
}

//enum允许编写抽象及实例方法，还允许每个enum实例各自实现方法
public enum WeekDayEnum {
    SUN(0) {
      	//通过匿名内部类方式
        @Override
        public void showName() {
			System.out.println("sunday");
        }
    },MON(1) {
        @Override
        public void showName() {
			System.out.println("monday");
        }
    };
    private int index;
    WeekDayEnum(int index) {
        this.index = index;
    }
    public abstract void showName();
}
```

java 的枚举其实是一个语法糖，就是一个完整的类，只是向用户暴露几个`final static`的常量，隐藏掉实现细节而已。 

> 1. 枚举的构造器只能是 private 的，所以枚举也可以用来实现单例。
> 2. switch 支持枚举。