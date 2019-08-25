### 泛型类
泛型类定义时只需要在类名后面加上类型参数即可，当然你也可以添加多个参数，类似于<K,V>,<T,E,K>等。
```java
public class Generic<T>{ 
    private T t;

    public Generic(T t) { //泛型构造方法形参t的类型也为T，T的类型由外部指定
        this.t= t;
    }

    public T getT(){
        return t;
    }
}
```
### 泛型接口

```java
//定义一个泛型接口
public interface Generator<T> {
    public T next();
}
```
- 泛型接口未传入泛型实参时，与泛型类的定义相同，在声明类的时候，需将泛型的声明也一起加到类中。
`class FruitGenerator<T> implements Generator<T>`
- 如果泛型接口传入类型参数时，实现该泛型接口的实现类，则所有使用泛型的地方都要替换成传入的实参类型。
`class DataHolder implements Generator<String>`

### 泛型方法
泛型方法既可以存在于泛型类中，也可以存在于普通的类中。如果使用泛型方法可以解决问题，那么应该尽量使用泛型方法。
public与返回值中间非常重要，可以理解为声明此方法为泛型方法。
```java
public <T> void PrinterInfo(T e) {
    System.out.println(e);
}
```
**静态方法无法访问类上定义的泛型，如果在类中定义使用泛型的静态方法，需要添加额外的泛型声明（将这个方法定义成泛型方法。）**

### 类型通配符
`public static void getData(List<?> data) `
类型通配符一般是使用`?`代替具体的类型参数。例如`List<?>`在逻辑上是`List<String>`,`List<Integer>`等所有`List<具体类型实参>`的父类。
- 上界通配符<? extends T>
如 List<? extends Number>来定义，如此定义就是通配符泛型值接受Number及其下层子类类型。
- 下界通配符<? super T>>
如 List<? super Number>来定义，表示类型只能接受Number及其三层父类类型，如 Object 类型的实例