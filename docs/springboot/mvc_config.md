### SpringMVC自动配置

Spring Boot为Spring MVC提供**自动配置**，适用于大多数应用程序。

自动配置在Spring的默认值之上添加了以下功能：

- 包含`ContentNegotiatingViewResolver`和`BeanNameViewResolver`组件。
- 支持提供静态资源，包括对WebJars的支持。
- 自动注册`Converter`，`GenericConverter`和`Formatter`组件。
- 支持`HttpMessageConverters`。
- 自动注册`MessageCodesResolver`。
- 静态`index.html`支持。
- 自定义`Favicon`支持。
- 自动使用`ConfigurableWebBindingInitializer`bean。

### SpringMVC扩展配置

如果您想保留Spring Boot MVC功能并且想要添加其他额外的MVC配置（拦截器，格式化程序，视图控制器和其他功能），您可以添加自己的`@Configuration`类型的`WebMvcConfigurer`配置类，但**不要**使用 `@EnableWebMvc`，会让SpringBoot对Springmvc的所有自动配置都失效。

如果你希望提供

- RequestMappingHandlerMapping 

-  RequestMappingHandlerAdapter 

-  ExceptionHandlerExceptionResolver 

自定义实例可以声明WebMvcRegistrationsAdapter实例来提供这些组件。

```java
/**
	有一些教程是用的“WebMvcConfigurerAdapter”接口，不过在spring5.0版本后这个类被丢弃
	WebMvcConfigurerAdapter  ，虽然还可以用，但是看起来不好。
*/
@Configuration
public WebMvcConfg  implements  WebMvcConfigurer { 
    
   /* 拦截器 */
    @Override
    public void addInterceptors(
        InterceptorRegistry registry) {
        registry.addInterceptor(new MyInterceptor()).addPathPatterns("/**");
    
    }  
       
    /**
    	视图控制
   		以前写SpringMVC的时候，如果需要访问一个页面，必须要写Controller类，
    	然后再写一个方法跳转到页面，感觉好麻烦，
    	其实重写WebMvcConfigurer中的addViewControllers方法即可达到效果了
    */
    @Override
    public void addViewControllers(
        ViewControllerRegistry registry) {
        	//aaa请求会映射到aaa界面
    		registry.addViewController("/aaa").setViewName("aaa");
    }

    /**
    	比如，我们想自定义静态资源映射目录的话，只需重写addResourceHandlers方法即可。
		注：如果继承WebMvcConfigurationSupport类实现配置时必须要重写该方法
	*/
	@Override
    public void addResourceHandlers(
        ResourceHandlerRegistry registry) {
    	registry.addResourceHandler("/**").addResourceLocations("classpath:/");
       
    }

     
    
    @Override
    public void addArgumentResolvers(
        List<HandlerMethodArgumentResolver> resolvers) {

    }

    /* 添加跨域映射 */
    @Override
    public void addCorsMappings(
        CorsRegistry registry) {

    }

    /* 配置视图解析器 */
    @Override
    public void configureViewResolvers(
        ViewResolverRegistry registry) {

    }

    //还有很多方法..
    
}
```