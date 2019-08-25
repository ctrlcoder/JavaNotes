### 1 概述
- enum 与 class、interface 具有相同地位；
- 默认继承 java.lang.Enum 类，所以不能继承其他父类；其中 java.lang.Enum 类实现了 java.lang.Serializable 和 java.lang.Comparable 接口；
- 使用 enum 定义，默认使用 final 修饰，因此不能派生子类；
- 构造器默认使用 private 修饰，且只能使用 private 修饰；
- 枚举类的所有实例必须在枚举类的第一行显式列出，否则这个枚举类永远都不能产生实例。
- 列出这些实例时，系统会自动添加public static final 修饰，无须显式添加。

### 2 使用
在JDK 1.5之前，定义常量，我们一般使用两种方式：
- 定义一个常量类
- 定义一个常量接口

>java枚举类在JDK 1.5引入的，枚举类在项目中已经不可或缺。
>
>正是因为Java枚举类可以有自定义的方法，可以实现接口、定义抽象类等，更加的灵活，已经被广大开发人员推荐在项目中使用 -- **使用枚举类替换接口常量或者类常量**等。



#### 2.1 无构造函数的枚举
> 如果枚举中没有定义方法，也可以在最后一个实例后面加逗号、分号或什么都不加。

```java
public enum Season {
    SPRING,
    SUMMER,
    FALL,
    WINTER
}
```

可以结合`switch`使用


```java
public class Main {

    public static void main(String[] args) {
        Season season = Season.SPRING;
        switch (season) {
            case SPRING:
                System.out.println("Spring");
                break;
            case SUMMER:
                System.out.println("Summer");
                break;
            case FALL:
                System.out.println("Fall");
                break;
            case WINTER:
                System.out.println("Winter");
                break;
            default:
                break;
        }
    }
}
```

### 2.2 有构造函数的枚举
```java
public enum ProductType {

    SMART_HOME(0, "智能家居"),
    HEALTH_CARE(1, "医疗健康"),
    MOTION_DETECTION(2, "运动检测"),
    INDUSTRIAL_PRODUCTION(3, "工业生产"),
    ENVIRONMENT_MONITORING(4, "环境监测"),
    INTELLIGENT_OFFICE(6, "智能办公"),
    LOCATION_DEVICE(7, "定位器/防丢器"),
    SMART_GATEWAY(8, "智能网关"),
    OTHERS(5, "其它");

    private int code;

    private String name;

    private ProductType(int code, String name) {
        this.code = code;
        this.name = name;
    }


    public int getCode() {
        return code;
    }


    public String getName() {
        return name;
    }

}
```

### 3 枚举的方法
在 enum 中，提供了一些基本方法：
- int compareTo(E o)： 该方法用于与制定枚举对象比较顺序，同一个枚举实例只能与相同类型的枚举实例比较。如果该枚举对象位于指定枚举对象之后，则返回正整数；反之返回负整数；否则返回零。
- static values()： 返回一个包含全部枚举值的数组，可以用来遍历所有枚举值。
- String name()： 返回此枚举实例的名称，即枚举值。
- ordinal()：返回实例声明时的次序，从 0 开始。
- static valueOf()： 返回带指定名称的指定枚举类型的枚举常量，名称必须与在此类型中声明枚举常量所用的标识符完全匹配
- boolean equals()方法： 比较两个枚举类对象的引用。
    - 枚举类型对象之间的值比较，是可以使用==，直接来比较值，是否相等的，不是必须使用equals方法的哟

### 3 其他
- 实现接口
- 包含抽象方法
- 包含静态变量和静态方法
- 使用接口组织枚举
- 枚举集合

> https://my.oschina.net/wangmengjun/blog/827189