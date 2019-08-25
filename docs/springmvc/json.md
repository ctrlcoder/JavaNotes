## 返回统一的数据结构
一般服务器传回来的 JSON 格式是这样：
```json
{
    "code": xx,
    "message": xx,
    "data": xxx
}
```
**需要定义一个 JavaBean**

```java
public class BaseResult<T> implements Serializable {


    //0成功，-1失败
    private Integer code;
    private String msg;
    private T data;//Object也可以

  //省略get set方法

    public BaseResult() {
    }

    public BaseResult(Integer code, String msg) {
        this.code = code;
        this.msg = msg;
    }

    public BaseResult(Integer code, String msg, T data) {
        this.code = code;
        this.msg = msg;
        this.data = data;
    }

    public static <T> BaseResult<T> success(T t) { return new BaseResult<T>(0, "SUCCESS", t); }


    public static <T> BaseResult<T> fail(String message) { return new BaseResult<T>(-1, message); }
}
```

## 返回JSON数据
SpringMVC 中使用 `@ResponseBody` 返回 JSON 时，默认采用 jackson 解析。

> 需要手动添加依赖包：jackson-core、jackson-databind、jackson-annotation 。

**自动转换成JSON格式**，回传：

```java
@RequestMapping(value = "/test")
@ResponseBody
    public BaseResult<Object> test() {
        try {
            Item item = new Item();
            item.setId("12345");
            item.setTitle("测试");
            item.setCategory(1);
            return BaseResult.success(item);
        }catch (Exception e){
            return  BaseResult.fail(e.getMessage());
        }
    }
```
前端显示的json：
```json
{
    "code": 0,
    "msg": "SUCCESS",
    "data": {
        "id": "12345",
        "title": "测试",
        "category": 1
    }
}
```

### jackson的配置选项
```properties
spring.jackson.serialization.indent-output=true #格式化打印
spring.jackson.serialization-inclusion=non_null #去除 NULL 字段
```

## 接受JSON数据
### 以实体类接收
>@RequestBody 注解常用来处理 content-type 不是默认的 application/x-www-form-urlcoded 编码的内容。一般情况下来说常用其来处理 application/json 类型。

```java
////这种方式下所有的参数都只能封装在User对象中，不能单独设置参数   
@RequestMapping(value = "/test",method = RequestMethod.POST)
    public BaseResult<Object> test(@RequestBody Item item) {
        try {
            itemService.addOne(item);
            return BaseResult.success(item);

        }catch (Exception e){
            return  BaseResult.fail(e.getMessage());
        }
    }
```


### 以map方式接收
```java
    @RequestMapping(value = "/test",method = RequestMethod.POST)
    public BaseResult<Object> test(@RequestBody  Map<String,Object> item){ ... }
```

### 以字符串方式接受
**使用@RequestBody注解的时候，只接受JSON字符串而不是JSON对象**

```java
import com.alibaba.fastjson.JSONObject;
    @RequestMapping(value = "/test",method = RequestMethod.POST)
        public BaseResult<Object> test(@RequestBody String param){
    		JSONObject json = JSON.parseObject(param); }
```
## 前端JSON
如果想以 JSON 格式把数据提交到后台的话，JSON.stringify() 必须有，否则只会当做表单提交
- JSON对象:`var str2 = { "name": "deluyi", "sex": "man" };`
- JSON字符串:`var str1 = '{ "name": "deyuyi", "sex": "man" }';`
>  JSON 本身就是文本格式（字符串）的， 与其他程序进行数据交换时，传递的就是 字符串。

- **注意**： 
- 当Ajax以application/x-www-form-urlencoded格式上传即使用JSON对象，后台需要使用@RequestParam 或者Servlet获取。 
- 当Ajax以application/json格式上传即使用JSON字符串，后台需要使用@RquestBody获取

