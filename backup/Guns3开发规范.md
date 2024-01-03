2024-01-03

### 1. 强依赖关系

项目基于spring boot架构，所以对spring boot强依赖，另外对hutool工具类，lombok，fastjson强依赖，其他框架不强依赖

### 2. 任何类均可拓展

利用@Primary注解来替换已经装载的spring容器中的bean

### 3.每个模块要有一个常量类

常量类用来存放模块名称和异常枚举的步进值，如果本模块异常较多，可以存放多个步进值

```java
public interface RuleConstants {

    /**
     * 规则模块的名称
     */
    String RULE_MODULE_NAME = "kernel-a-rule";

    /**
     * 异常枚举的步进值
     */
    String RULE_EXCEPTION_STEP_CODE = "00";

}
```

### 4. 每个模块要有一个异常类

异常类要继承ServiceException

```java
public class DaoException extends ServiceException {

    public DaoException(AbstractExceptionEnum exception) {
        super(DbConstants.DB_MODULE_NAME, exception);
    }

}
```

### 5. expander包是对配置表的拓展

kernel-d-config模块只负责对系统配置的初始化，新增，删除等操作，不进行对某个具体配置的维护，各个模块需要配置拓展属性时，在各个模块的api模块建立expander包维护

### 6. Bean的装配，尽量在类的构造函数，不要在类的内部用@Resource或者@Autowired

构造函数装配更灵活，如果直接用@Resource则会交给spring去装配，spring会去找到容器中的相关bean，不如手动的灵活

多出现在装配的是接口的情况，如果接口有多个实现，很明显用构造函数去装配更合适

### 7. pojo的分包结构

pojo下可以分为request（控制器层请求参数的封装），response（控制器层响应参数的封装），param（其他类下参数的封装）

其中request包下的类以Request结尾，response包下的类以Response结尾，param包下的类以Param结尾

request包下的类一般会加上参数校验注解，参数校验用的hibernate validator注解

一般情况，直接用实体返回，减少一些pojo的书写，复杂的返回对象还是要单独封装pojo

### 8，pom中的注释要写清楚，为什么引用这个模块写到每个依赖上边

例如，如下写法：

```xml
<!--jwt模块-->
<!--用来给生成带鉴权的url用-->
<dependency>
    <groupId>com.sedinbj.kernel</groupId>
    <artifactId>jwt-sdk</artifactId>
    <version>1.0.0</version>
</dependency>
```

### 9. @ApiResource 上的 path url 写完整的请求url 不要写到类上url

以下是错误示例

```java
@RestController
@ApiResource(name = "配置管理", path = "/system/config/")
public class SysConfigController {

    @GetResource(name = "获取配置列表", path = "/list")
    public ResponseData pageList() {
        return new SuccessResponseData();
    }
    
}
```

### 10. private方法要放到方法最下边

在类中 private 方法放到最下边

```java
public class ConfigServiceImpl implements ConfigService {

    public List<Long> getList() {
        return null;
    }

    public Object getDeatil() {
        return null;
    }

    private Object getOne() {
        return null;
    }

}
```

### 11. Controller 中 post get 规范

在 Controller 类中所有不涉及到数据变动的接口，例如分页查询、根据主键获取详情等查询接口使用 @GetResource 注解，其余添加、修改、删除接口统一使用 @PostResource 注解 例如

```java

@RestController
@ApiResource(name = "配置管理", path = "/system/config/")
public class SysConfigController {

    @GetResource(name = "获取配置列表", path = "/list")
    public ResponseData pageList() {
        return new SuccessResponseData();
    }

    @PostResource(name = "添加配置", path = "/add")
    public ResponseData add() {
        return new SuccessResponseData();
    }

    @PostResource(name = "修改配置", path = "/update")
    public ResponseData update() {
        return new SuccessResponseData();
    }

    @PostResource(name = "删除配置", path = "/delete")
    public ResponseData delete() {
        return new SuccessResponseData();
    }

}
```

### 12. api，枚举，异常等类创建后就尽快提交

创建好后直接提交，为了方便别人用，不用别人再创建，防止重复，在创建之前，先拉取一下代码看看别人是否创建了

### 13. 请求参数要加@ChineseDescription注解


### 14. 请求参数类要继承BaseRequest，实体类继承BaseEntity，Controller方法响应结果统一使用SuccessResponseData封装，若有错误需抛出有实际错误信息的异常


### 15. service层方法的顺序，先写增改删操作，最后写查询操作

service接口和service实现的顺序要一致

```java
@GetResource(name = "查看详情", path = "/company/detail", responseClass = CompanyResponse.class)
```

### 16. 为了方便加业务缓存，所有的dao操作不要直接用xxxMapper

统一放在service操作dao，别的层不能直接调用xxxMapper类中的方法。





