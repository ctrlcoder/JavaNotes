## .1.常用注解

### Bean相关

- @Autowired：按照类型注入
  - @Qualifie：在自动按照类型注入的基础之上，再按照 Bean 的 id 注入
- @Service：业务逻辑层
- @Repository：数据访问层
- @Controller：控制器层
- @Component：表示 Bean
- @ComponentScan：用于对Component进行扫描
- @Configuration：声明当前类为配置类，
- @Bean：注解在方法上，声明当前方法的返回值为一个bean

### Spring MVC相关

- @RestController

- @RequestMapping

- @ResponseBody

- @RequestBody

- @RequestParam 
- @PathVariable

## 2. IOC和AOP

- 对象的实例不再由调用者来创建，而是由 Spring 容器来创建，Spring 容器会负责控制程序之间的关系，而不是由调用者的程序代码直接控制。这样，控制器由应用代码转移到了Spring容器，控制权发生了反转，这就是Spring的**控制反转**。
- Spring 容器负责将被依赖对象赋值给调用者的成员变量，这相当于为调用者注入了它依赖的实例，这就是Spring 的**依赖注入**。
- AOP 采用横向抽取机制，将分散在各个方法中的重复代码抽取出来，然后在程序编译或运行时，再将这些提出的代码应用到需要执行的地方。

## 3.Spring MVC流程

1. 客户端通过 url 发送请求
2. **核心控制器**Dispatcher Servlet接收到请求，通过**处理器映射器 **HandlerMapping 找到对应的 handler，并将 url 映射的控制器 controller 返回给核心控制器。
3. 通过**核心控制器**找到系统或默认的**处理器适配器 **HandlAdapter。
4. 由找到的**适配器**，调用实现对应接口的**处理器**Handler，并将 **ModelAndView**（数据和视图结合的对象）结果返回给**适配器**，再返回给**核心控制器**。
5. **核心控制器**将获取的 ModelAndView 对象传递给 **View Resolver** 视图解析器，解析后返回具体 View（视图）。
6. **核心控制器**对 View 进行渲染（即将模型数据填充至视图中）
7. 视图渲染结果会返回给客户端浏览器显示。