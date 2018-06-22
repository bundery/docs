# 接口

接口内的成员可包含：`static nest-classes`、`nest-interfaces`、`static nest-enum`、`public static final fields`、`pubic abstract method`、`default public method`（>=java 8）、`public static methods`（>=java 8）

## default方法

java 8 引入了接口的 Default Methods，最初的设计目的是让已经存在的接口可以更好的扩展，这样添加新方法就不需要在原本已经存在的实现该接口的类做任何改变，如果没有默认方法，在接口新添加方法之后，所有实现该接口的类都需要修改。

```java
interface InterfaceA {
    default void foo() {
        System.out.println("InterfaceA foo");
    }
}

//继承了foo方法
class ClassA implements InterfaceA {
}

public class Test {
    public static void main(String[] args) {
        new ClassA().foo(); // 打印：“InterfaceA foo”
    }
}
```

## 组织枚举

```java
public interface FoodInterfaceEnum {
    enum Noodles implements FoodInterfaceEnum{
        BEEF_Noodles,EGG_Noodles
    }
    enum Bread implements FoodInterfaceEnum{
        BUTTER_BREAD,BACON_BREAD
    }
}
```

