- @Component ：注册为 Spring 管理的 Bean 
  - @Repository 持久层
  - @Service 业务层
  - @Controller 控制器层

## 注入Bean

> http://www.dengshenyu.com/spring/2016/10/09/spring-inject.html
>
> 一般来说，一个接口就一个实现类的话，按类型注入就可以，如果多个实现类，需要按照名称注入

- @Autowired 按照类型注入
  - @Qualifier
    - 在自动按照类型注入的基础之上，再按照 Bean 的 id 注入
    - 必须和 @Autowired 一起使用；但是给方法参数注入时，可以独立使用
- @Inject  JSR-330提供的注解和@Autowired完全相同，@Resource JSR-250提供的注解
> 执行顺序：
@Autowired和@Inject
按照类型匹配
使用限定符进行类型限定
按照名称匹配
@Resource
按照名称匹配
按照类型匹配
使用限定符进行类型限定（但如果名称匹配成功的话这条会被忽略）

### @Resource

它的作用相当于 @Autowired，只不过 @Autowired 按 byType 自动注入，@Resource 默认按 byName 自动注入，当找不到与名称匹配的bean才会按类型装配

##### 相同点：
@Resource的作用相当于@Autowired，均可标注在字段或属性的setter方法上。

##### 不同点：
- 提供方：@Autowired是由org.springframework.beans.factory.annotation.Autowired提供，换句话说就是由Spring提供；@Resource是由javax.annotation.Resource提供，即J2EE提供，需要JDK1.6及以上。
- 注入方式：@Autowired只按照byType 注入；@Resource默认按byName自动注入，也提供按照byType 注入；
- 属性：@Autowired按类型装配依赖对象，默认情况下它要求依赖对象必须存在，如果允许null值，可以设置它required属性为false。如果我们想使用按名称装配，可以结合@Qualifier注解一起使用。
- @Resource注解的使用性更为灵活，可指定名称，也可以指定类型 ；@Autowired注解进行装配容易抛出异常，特别是装配的bean类型有多个的时候，而解决的办法是需要在增加@Qualifier进行限定。

### @PostConstruct 和 @PreDestroy
JSR-250 为初始化之后/销毁之前方法的指定定义了两个注解类，分别是 @PostConstruct 和 @PreDestroy，这两个注解只能应用于方法上。标注了 @PostConstruct 注解的方法将在类实例化后调用，而标注了 @PreDestroy 的方法将在类销毁之前调用。

>@Resource、@PostConstruct 以及@PreDestroy。它们不属于 Spring 框架，而是在 Java 中定义的。只不过 Spring 框架支持这种 Java注解。


###  @Configuration 和 @Bean
Spring的官方团队说 @Component可以替代 @Configuration注解；@Bean注解只能写在方法上，表明使用此方法创建一个对象，并且放入 Spring 容器。
```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component  //看这里！！！
public @interface Configuration 
```
##### 区别：
@Bean 注解主要用于方法上，有点类似于工厂方法，当使用了 @Bean 注解，我们可以连续使用多种定义bean时用到的注解，譬如用 @Qualifier 注解定义工厂方法的名称，用 @Scope 注解定义该 bean 的作用域范围，譬如是 singleton 还是 prototype 等。
Spring 中新的 Java 配置支持的核心就是 @Configuration 注解的类。这些类主要包括 @Bean 注解的方法来为 Spring 的 IoC 容器管理的对象定义实例，配置和初始化逻辑。
使用 @Configuration 来注解类表示类可以被 Spring 的 IoC 容器所使用，作为 bean 定义的资源。
```java
/*
@Configuation等价于<Beans></Beans>
@Bean等价于<Bean></Bean>
*/
@Configuration
public class AppConfig {
    @Bean
    public MyService myService() {
        return new MyServiceImpl();
    }
}
```
这和 Spring 的 XML 文件中的非常类似
```xml
<beans>
    <bean id="myService" class="com.acme.services.MyServiceImpl"/>
</beans>
```
##### 举例对比说明
>在@Component类中使用方法或字段时不会使用CGLIB增强(及不使用代理类：调用任何方法，使用任何变量，拿到的是原始对象，后面会有例子解释)。而在@Configuration类中使用方法或字段时则使用CGLIB创造协作对象（及使用代理：拿到的是代理对象）;当调用@Bean注解的方法时它不是普通的Java语义，而是从容器中拿到由Spring生命周期管理、被Spring代理甚至依赖于其他Bean的对象引用。在@Component中调用@Bean注解的方法和字段则是普通的Java语义，不经过CGLIB处理。

```java
@Configuration
public static class Config {

    @Bean
    public SimpleBean simpleBean() {
        return new SimpleBean();
    }

    @Bean
    public SimpleBeanConsumer simpleBeanConsumer() {
        return new SimpleBeanConsumer(simpleBean());
    }
}
```

- 第一个代码正常工作，正如预期的那样，`SimpleBeanConsumer`将会得到一个单例`SimpleBean `的链接。
- 第二个配置是完全错误的，因为 Spring 会创建一个`SimpleBean`的单例 bean，但是`SimpleBeanConsumer`将获得另一个`SimpleBean`实例（也就是相当于直接调用`new SimpleBean()`，这个 bean 是不归 Spring 管理的），既`new SimpleBean()`实例是 Spring 上下文控件之外的。

```java
@Component
public static class Config {

    @Bean
    public SimpleBean simpleBean() {
        return new SimpleBean();
    }

    @Bean
    public SimpleBeanConsumer simpleBeanConsumer() {
        return new SimpleBeanConsumer(simpleBean());
    }
}
```

#### 原因总结
使用 @Configuration，所有标记为 @Bean的方法将被包装成一个 CGLIB 包装器，它的工作方式就好像是这个方法的第一个调用，那么原始方法的主体将被执行，最终的对象将在 Spring 上下文中注册。所有进一步的调用只返回从上下文检索的 bean。
在上面的第二个代码块中，新的`SimpleBeanConsumer(simpleBean())`只调用一个纯 java 方法。为了纠正第二个代码块，我们可以这样做
```java
@Component
public static class Config {
    @Autowired
    SimpleBean simpleBean;

    @Bean
    public SimpleBean simpleBean() {
        return new SimpleBean();
    }

    @Bean
    public SimpleBeanConsumer simpleBeanConsumer() {
        return new SimpleBeanConsumer(simpleBean);
    }
}
```

### @ComponentScan 
用于指定 Spring 在初始化容器时要扫描的包,用注解标识的类会被 Spring 自动扫描并且装入 Bean 容器中，作用和在 Spring 的 xml 配置文件中的：`<context:component-scan base-package="com..."/>`是一样的。
- 有注解（开启扫描）
  - 有路径：扫描指定路径
  - 没路径：默认扫描当前配置类所在包及其子包下组件
- 没有注解（不开启扫描）

- 使用 includeFilters 来按照规则只包含某些包的扫描
- 使用 excludeFilters 来按照规则排除某些包的扫描

#### context:annotation-config和context:component-scan
`<context-annotation-config/>`该配置的主要作用是“激活”已声明的 bean，即“激活” spring 容器内配置的 bean。
如：在 spring 容器内配置了`<bean name="demo" class="com.example.Demo"></bean>`，那么则可以使用 @Autowired 注解将 demo 注入相应对象中。但是，该配置对@Component、@Controller、@Service、@Repository注解的，但没有在 spring 容器注册过的bean无效。

`<context:component-scan base-package="" />`该配置包含了`<context-annotation-config />`配置的作用，与之不同的是：该配置可以扫描 base-package 指定包下@Component、@Controller、@Service、@Repository 注解并将被注解 bean 注册到 spring 容器内，使之生效。
**注意**：1. 当这两个配置同时配置时，`<context-annotation-config />`将失效，以`<context:component-scan base-package="" />`为准。2. @Component、@Controller、@Service、@Repository这些注解本身并不具有声明注册bean的功能，在没有`<context:component-scan>`扫描解析之前是没有任何作用的。


### @PropertySource
注解在类上，指定文件地址注入配置文件

### @Value
注解在变量上，调用资源（普通文件，网址，配置文件，系统环境变量等）


### @Import
用于导入其他配置类，在引入其他配置类时，可以不用再写@Configuration 注解。当然，写上也没问

### @profile
用来标明当前运行环境的注解

