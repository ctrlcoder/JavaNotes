## 概述
RESTEasy是JBoss的一个开源项目，提供各种框架帮助你构建RESTful Web Services和RESTful Java应用程序。它是JAX-RS规范的一个完整实现并通过JCP认证。
JAX-RS提供了一些标注将一个资源类，一个POJOJava类，封装为Web资源。

**标注包括**：
- @Path，标注资源类或方法的相对路径；
- @GET，@PUT，@POST，@DELETE，标注方法是用的HTTP请求的类型；
- @Produces，标注返回的MIME媒体类型
- @Consumes，标注可接受请求的MIME媒体类型；
- @PathParam，@QueryParam，@HeaderParam，@CookieParam，@MatrixParam，@FormParam
分别标注方法的参数来自于HTTP请求的不同位置，例如@PathParam来自于URL的路径，@QueryParam来自于URL的查询参数，@HeaderParam来自于HTTP请求的头信息，@CookieParam来自于HTTP请求的Cookie。

## JAX-RS
JAX-RS 是代表restful web service的一套规范API，JAX-RS规范基于JAVA编程语言，它是用来创建Restful 风格的web services服务的。Jax-rs使用一系列注解来简化java开发。
JAX-RS也称jsr339 (全称java specifications requests java规范提案第339个)，由jcp(java community process)组织经过投票通过。

## RESTful API
它是一种互联网应用程序的API设计理念：URL定位资源，用HTTP动词（GET,POST,DELETE,DETC）描述操作。
**常用的HTTP动词有下面五个：**
- GET：读取（Read）
- POST：新建（Create）
- PUT：更新（Update）
- PATCH：更新（Update），通常是部分更新
- DELETE：删除（Delete）
```
GET /blog/getArticles --> GET /blog/Articles  获取所有文章
GET /blog/addArticles --> POST /blog/Articles  添加一篇文章
GET /blog/editArticles --> PUT /blog/Articles  修改一篇文章 
GET /rest/api/deleteArticles?id=1 --> DELETE /blog/Articles/1  删除一篇文章
```


## RESTEasy？
> 简单地说，resteasy核心就是一个servlet前端控制器，理念都一样。
> 应该来说，resteasy的优势在于相比spring mvc而言，更加的专注，简单。就大型应用而言，绝对是spring mvc占据优势。
> 就简洁性而言，应该来说在spring 4.0引入spring restcontroller之后，优势就没有那么明显了，应该来说3.2之前对restful还不那么有信心。
> 就像我们现在发现基本上mvc上spring mvc占据了绝大部分的新项目，估计以后resteasy也会小众化。
> 至于说resteasy为什么活下来，感觉上spring社区有个比较明显的倾向就是好像不怎么吃螃蟹，等到趋势比较明朗之后，要么就整合对方，要么就强行自己引入一套接口玩死对方。
> resteasy是JAX-RS的参考实现之一，规范制定者是核心人员之一，所以概念和属于上基本上完全各种restful service教材上能够直接对应。spring mvc方面，就没有那么直接的对应了，估计是鄙视J2EE的一种商业手法。
> 就应用而言，如果restful作为rpc的实现机制，resteasy应该是有优势的，毕竟集成没有spring mvc那么多配置（当然spring cloud除外），但作为应用，估计干不过spring mvc。
> 使用resteasy，最好使用servlet 3.0以上的容器，比如tomcat 7+。

## springboot + resteasy依赖

demo：https://github.com/paypal/resteasy-spring-boot

```xml
<dependency>
    <groupId>com.paypal.springboot</groupId>
    <artifactId>resteasy-spring-boot-starter</artifactId>
    <version>2.3.4-RELEASE</version>
</dependency>
```
