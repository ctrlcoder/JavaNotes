> 我们经常要使用 @Autowired 注解注入 Service 或者 Dao 接口，在 Service 层中注入其它的 Service 接口或者 Dao 接口都是可以的，但是如果我们要在我们自己封装的 Utils 工具类中或者非 Controller 普通类中使用 @Autowired 注解注入Service 或者 Dao 接口，直接注入是不可能的，因为 Utils 使用了静态的方法，我们是无法直接使用非静态接口的，当我们遇到   的问题，我们就要想办法解决了。

```java
@Component
public class TestUtil {

    @Autowired
    private ItemRepository itemRepository;

    public  static  TestUtil testUtil;    

    @PostConstruct
    public  void  init(){
        testUtil = this;

    }    
    public static  void  test(){
        System.out.println(testUtil.itemRepository.findAll());
    }
}}
```

将 class DownLoadUtils 上添加 @Component 声明其为bean组件。
使用 @Autowired注入启动类。
在 DownLoadUtils 中声明一个 静态的私有变量 private static DownloadUtils downloadUtils;
添加共有的 init 方法，并用 @PostConstruct 声明在项目启动的时候进行执行该方法，也可以理解为在 spring容器初始化的时候执行该方法

#### 一开始在本类和其他静态类调用，一直报空指针异常，在SpringBoot主程序里则调用成果。还有其他方法，待学习。

