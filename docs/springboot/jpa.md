## 概念
JPA(Java Persistence API)是Sun官方提出的Java持久化规范。它为Java开发人员提供了一种对象/关联映射工具来管理Java应用中的关系数据。他的出现主要是为了简化现有的持久化开发工作和整合ORM技术，结束现在Hibernate，TopLink，JDO等ORM框架各自为营的局面。值得注意的是，JPA是在充分吸收了现有Hibernate，TopLink，JDO等ORM框架的基础上发展而来的，具有易于使用，伸缩性强等优点。从目前的开发社区的反应上看，JPA受到了极大的支持和赞扬，其中就包括了Spring与EJB3.0的开发团队。

> 注意:JPA是一套规范，不是一套产品，那么像Hibernate,TopLink,JDO他们是一套产品，如果说这些产品实现了这个JPA规范，那么我们就可以叫他们为JPA的实现产品。

Spring Data JPA 是 Spring 基于 ORM 框架、JPA 规范的基础上封装的一套JPA应用框架，可使开发者用极简的代码即可实现对数据的访问和操作。它提供了包括增删改查等在内的常用功能，且易于扩展！学习并使用 Spring Data JPA 可以极大提高开发效率！

## 基本查询
### 简单查询
Spring Data JPA 默认预先生成了一些基本的CURD的方法，例如：增、删、改等等
**1 继承JpaRepository**
要实现有两个泛型参数，第一个T表示实体类，第二个表示主键的类型
```
public interface UserRepository extends JpaRepository<User, Long> {
}
```
**2 使用默认方法**
```java
@Test
public void testBaseQuery() throws Exception {
    User user=new User();
    userRepository.findAll();
    userRepository.findOne(1l);
    userRepository.save(user);
    userRepository.delete(user);
    userRepository.count();
    userRepository.exists(1l);
    // ...
}
```
### 自定义简单查询
自定义的简单查询就是根据方法名来自动生成SQL，主要的语法是`findXXBy`,`readAXXBy`,`queryXXBy`,`countXXBy`, `getXXBy`后面跟属性名称：

```java
User findByUserName(String userName);复制代码
```

也使用一些加一些关键字`And`、 `Or`

```java
User findByUserNameOrEmail(String username, String email);复制代码
```

修改、删除、统计也是类似语法

```java
Long deleteById(Long id);

Long countByUserName(String userName)复制代码
```

基本上SQL体系中的关键词都可以使用，例如：`LIKE`、 `IgnoreCase`、 `OrderBy`。

```java
List<User> findByEmailLike(String email);

User findByUserNameIgnoreCase(String userName);

List<User> findByUserNameOrderByEmailDesc(String email);
```
**具体的关键字，使用方法和生产成SQL如下表所示**

| Keyword           | Sample                                  | JPQL snippet                                                 |
| ----------------- | --------------------------------------- | ------------------------------------------------------------ |
| And               | findByLastnameAndFirstname              | … where x.lastname = ?1 and x.firstname = ?2                 |
| Or                | findByLastnameOrFirstname               | … where x.lastname = ?1 or x.firstname = ?2                  |
| Is,Equals         | findByFirstnameIs,findByFirstnameEquals | … where x.firstname = ?1                                     |
| Between           | findByStartDateBetween                  | … where x.startDate between ?1 and ?2                        |
| LessThan          | findByAgeLessThan                       | … where x.age < ?1                                           |
| LessThanEqual     | findByAgeLessThanEqual                  | … where x.age ⇐ ?1                                           |
| GreaterThan       | findByAgeGreaterThan                    | … where x.age > ?1                                           |
| GreaterThanEqual  | findByAgeGreaterThanEqual               | … where x.age >= ?1                                          |
| After             | findByStartDateAfter                    | … where x.startDate > ?1                                     |
| Before            | findByStartDateBefore                   | … where x.startDate < ?1                                     |
| IsNull            | findByAgeIsNull                         | … where x.age is null                                        |
| IsNotNull,NotNull | findByAge(Is)NotNull                    | … where x.age not null                                       |
| Like              | findByFirstnameLike                     | … where x.firstname like ?1                                  |
| NotLike           | findByFirstnameNotLike                  | … where x.firstname not like ?1                              |
| StartingWith      | findByFirstnameStartingWith             | … where x.firstname like ?1 (parameter bound with appended %) |
| EndingWith        | findByFirstnameEndingWith               | … where x.firstname like ?1 (parameter bound with prepended %) |
| Containing        | findByFirstnameContaining               | … where x.firstname like ?1 (parameter bound wrapped in %)   |
| OrderBy           | findByAgeOrderByLastnameDesc            | … where x.age = ?1 order by x.lastname desc                  |
| Not               | findByLastnameNot                       | … where x.lastname <> ?1                                     |
| In                | findByAgeIn(Collection ages)            | … where x.age in ?1                                          |
| NotIn             | findByAgeNotIn(Collection age)          | … where x.age not in ?1                                      |
| TRUE              | findByActiveTrue()                      | … where x.active = true                                      |
| FALSE             | findByActiveFalse()                     | … where x.active = false                                     |
| IgnoreCase        | findByFirstnameIgnoreCase               | … where UPPER(x.firstame) = UPPER(?1)                        |

## 复杂查询
### 分页查询
分页查询在实际使用中非常普遍了，spring data jpa已经帮我们实现了分页的功能，在查询的方法中，需要传入参数Pageable,当查询中有多个参数的时候Pageable建议做为最后一个参数传入
```
Page<User> findALL(Pageable pageable);

Page<User> findByUserName(String userName,Pageable pageable);
```
Pageable 是spring封装的分页实现类，使用的时候需要传入页数、每页条数和排序规则
```
@Test
public void testPageQuery() throws Exception {
    int page=1,size=10;
    Sort sort = new Sort(Direction.DESC, "id");
    Pageable pageable = new PageRequest(page, size, sort);
    userRepository.findALL(pageable);
    userRepository.findByUserName("testName", pageable);
}
```

### 限制查询
有时候我们只需要查询前N个元素，或者支取前一个实体。

```java
ser findFirstByOrderByLastnameAsc();

User findTopByOrderByAgeDesc();

Page<User> queryFirst10ByLastname(String lastname, Pageable pageable);

List<User> findFirst10ByLastname(String lastname, Sort sort);

List<User> findTop10ByLastname(String lastname, Pageable pageable);
```

### 定义SQL查询
其实Spring data 觉大部分的SQL都可以根据方法名定义的方式来实现，但是由于某些原因我们想使用自定义的SQL来查询，spring data也是完美支持的；在SQL的查询方法上面使用@Query注解，如涉及到删除和修改在需要加上@Modifying.也可以根据需要添加 @Transactional 对事物的支持，查询超时的设置等
```
@Modifying
@Query("update User u set u.userName = ?1 where c.id = ?2")
int modifyByIdAndUserId(String  userName, Long id);

@Transactional
@Modifying
@Query("delete from User where id = ?1")
void deleteByUserId(Long id);

@Transactional(timeout = 10)
@Query("select u from User u where u.emailAddress = ?1")
User findByEmailAddress(String emailAddress);
```

### 多表查询

多表查询在spring data jpa中有两种实现方式，第一种是利用hibernate的级联查询来实现，第二种是创建一个结果集的接口来接收连表查询后的结果，这里主要第二种方式。

首先需要定义一个结果集的接口类。

```
public interface HotelSummary {

    City getCity();

    String getName();

    Double getAverageRating();

    default Integer getAverageRatingRounded() {
        return getAverageRating() == null ? null : (int) Math.round(getAverageRating());
    }

}复制代码
```

查询的方法返回类型设置为新创建的接口

```
@Query("select h.city as city, h.name as name, avg(r.rating) as averageRating "
        - "from Hotel h left outer join h.reviews r where h.city = ?1 group by h")
Page<HotelSummary> findByCity(City city, Pageable pageable);

@Query("select h.name as name, avg(r.rating) as averageRating "
        - "from Hotel h left outer join h.reviews r  group by h")
Page<HotelSummary> findByCity(Pageable pageable);复制代码
```

使用

```
Page<HotelSummary> hotels = this.hotelRepository.findByCity(new PageRequest(0, 10, Direction.ASC, "name"));
for(HotelSummary summay:hotels){
        System.out.println("Name" +summay.getName());
    }复制代码
```

> 在运行中Spring会给接口（HotelSummary）自动生产一个代理类来接收返回的结果，代码汇总使用`getXX`的形式来获取

## 定义实体类
```
@Entity
@Table(name = "portal_menu")
public class Menu implements {
	@Id
	@Column(name = "id", length = 128, nullable = false)
	private String id;// 主键
}
```
**常用注解：**
- @Entity 标识这个实体类是一个JPA实体，表名和实体名称不一致的情况下需要指定表名name

- @Table（name = "自定义的表名"）自定义设置这个实体类在数据库所对应的表名！

- @Id 把这个类里面所在的变量设置为主键Id。

- @GeneratedValue 设置主键的生成策略，这种方式依赖于具体的数据库，如果数据库不支持自增主键，那么这个类型是没法用的。
  - strategy表示主键生成策略，取值有：
    - IDENTITY：采用数据库ID自增长的方式来自增主键字段，Oracle 不支持这种方式。
    - AUTO： JPA自动选择合适的策略，是默认选项。
    - SEQUENCE：通过序列产生主键，通过@SequenceGenerator 注解指定序列名，MySql不支持这种方式。
    - TABLE：通过表产生主键，框架借由表模拟序列产生主键，使用该策略可以使应用更易于数据库移植。
  - generator:表示主键生成器的名称，这个属性通常和ORM框架相关，例如，Hibernate可以指定uuid等主键生成方式

- @Column（name = "自定义字段名"，length = "自定义长度"，nullable = "是否可以空"，unique = "是否唯一"，columnDefinition = "自定义该字段的类型和长度"）
表示对这个变量所对应的字段名进行一些个性化的设置，例如字段的名字，字段的长度，是否为空和是否唯一等等设置。

- @Transient 表示该属性并非一个到数据库表的字段的映射,ORM框架将忽略该属性

- @Enumerated（"需要定义存入数据库的类型”) 使用枚举的时候，我们希望数据库中存储的是枚举对应的String类型，而不是枚举的索引值，需要在属性上面添加@Enumerated(EnumType.STRING) 注解

- @Temporal注释用来指定java.util.Date或java.util.Calender属性与数据库类型date、time或timestamp中的那一种类型进行映射。
  - TemporalType.DATE（日期）2017-05-14 
  - TemporalType.TIME（时间） 15:12:49  
  - TemporalType.TIMESTAMP（日期和时间）  2017-05-14 15:12:49



## Repository相关接口
- CrudRepository：继承Repository接口，新增了一组CRUD相关的方法
- PagingAndSortingRepository：继承CrudRepository接口，新增了一组分页排序的相关方法
- JpaRepository：继承PagingAndSortRepository接口，新增了一组JPA规范的方法
- 有个不属于Repository继承体系的接口，也比较常用，它就是JpaSpecificationExecutor
JpaSpecificationExecutor：不属于Repository继承体系，有一组JPA Criteria查询相关方法

## application.properties
```
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=
```
- create：每次加载Hibernate时都会删除上一次生成的表（包括数据），然后重新生成新表，即使两次没有任何修改也会这样执行。适用于每次执行单测前清空数据库的场景。
- create-drop：每次加载Hibernate时都会生成表，但当SessionFactory关闭时，所生成的表将自动删除。
- update：最常用的属性，第一次加载hibernate时根据model类会自动建立起表的结构（前提是先建立好数据库），以后加载hibernate时根据model类自动更新表结构，即使表结构改变了但表中的行仍然存在不会删除以前的行。要注意的是当部署到服务器后，表结构是不会被马上建立起来的，是要等应用第一次运行起来后才会。
-  validate：每次加载Hibernate时都会验证数据表结构，只会和已经存在的数据表进行比较，根据model修改表结构，但不会创建新表。
- none：什么都不做