# MyBatis-Plus 核心笔记

## 一、Lambda 查询与更新

### Lambda 查询

java

运行

```java
LambdaQueryWrapper<UserExam> lambdaQuery = new LambdaQueryWrapper<>();
lambdaQuery.eq(UserExam::getUserId, userId)
          .eq(UserExam::getId, req.getId())
          .eq(UserExam::getTrainingId, req.getTrainingId());
UserExam userExam = lambdaQuery.one();
String remark = userExam.getRemark();
```

### Lambda 更新

java

运行

```java
LambdaUpdateWrapper<UserExam> lambdaUpdate = new LambdaUpdateWrapper<>();
lambdaUpdate.set(UserExam::getRemark, remark).update();
```

## 二、批量插入优化

* **配置参数**：`rewriteBatchedStatements = true`（开启后可提升批量插入性能）
* **对比方案**：
  * 普通 for 循环逐条插入：性能极差，不推荐。
  * IService 批量插入：基于 MyBatis-Plus 的 `IService`接口提供批量插入方法。
  * 开启 `rewriteBatchedStatements`参数：优化 JDBC 批量操作性能。

## 三、逻辑删除配置

在 `application.yml`中配置全局逻辑删除规则：

yaml

```yaml
mybatis-plus:
  global-config:
    db-config:
      logic-delete-field: del_flag  # 全局逻辑删除的字段名，类型支持boolean、integer
      logic-delete-value: 1         # 逻辑已删除值（默认1）
      logic-not-delete-value: 0     # 逻辑未删除值（默认0）
```

## 四、通用枚举配置

在 `application.yml`中配置全局枚举处理器：

yaml

```yaml
mybatis-plus:
  configuration:
    default-enum-type-handler: com.baomidou.mybatisplus.core.handlers.MybatisEnumTypeHandler
```

**枚举类示例**：

java

运行

```java
@Getter
public enum UserStatus {
    NORMAL(1, "正常"),
    FREEZE(2, "冻结");

    @EnumValue
    private final int value;
    private final String desc;

    UserStatus(int value, String desc) {
        this.value = value;
        this.desc = desc;
    }
}
```

## 五、核心注解与配置

### 主键生成策略

java

运行

```java
// 自动增长（数据库自增）
@TableId(value = "id", type = IdType.AUTO)

// 手动输入（需自己设置id）
@TableId(value = "id", type = IdType.INPUT)

// 分配ID（雪花算法生成）
@TableId(value = "id", type = IdType.ASSIGN_ID)

// 分配UUID
@TableId(value = "id", type = IdType.ASSIGN_UUID)
```

### 字段与表映射

java

运行

```java
// 指定数据库对应字段（is开头字段建议显式注解）
@TableField("blue_subsystem_id")

// 指定数据库表名
@TableName("blue_payload_setting")
```

### MyBatis-Plus 全局配置

yaml

```yaml
mybatis-plus:
  type-aliases-package: com.xxx.mp.domain.po  # 别名扫描包
  mapper-locations: "classpath:/mapper/**/*.xml"  # Mapper.xml文件地址
  configuration:
    map-underscore-to-camel-case: true  # 驼峰命名自动映射
    cache-enabled: true  # 二级缓存开关
  global-config:
    db-config:
      id-type: auto  # 全局主键策略
      update-strategy: not_null  # 更新策略：只更新非空字段
```

## 六、分页功能实现

### 分页 VO 类（BasePageVO）

java

运行

```java
public class BasePageVO<T> {
    protected long pageNum;     // 当前页
    protected long pageSize;    // 每页条数
    protected long totalPage;   // 总页数
    protected long total;       // 总条数
    protected List<T> list;     // 分页数据列表

    public BasePageVO() {}

    public BasePageVO(long pageNum, long pageSize, long total, List<T> list) {
        this.pageNum = pageNum;
        this.pageSize = pageSize;
        this.total = total;
        this.list = list;
        this.totalPage = getPages();
    }

    public BasePageVO(long pageNum, long pageSize, long totalPage, long total, List<T> list) {
        this.pageNum = pageNum;
        this.pageSize = pageSize;
        this.totalPage = totalPage;
        this.total = total;
        this.list = list;
    }

    protected long getPages() {
        if (getPageSize() == 0) {
            return 0L;
        }
        long pages = getTotal() / getPageSize();
        if (getTotal() % getPageSize() != 0) {
            pages++;
        }
        return pages;
    }

    // Getter和Setter方法省略...
}
```

### 分页查询示例

java

运行

```java
@Service
public class MessageServiceImpl extends ServiceImpl<MessageMapper, Message> implements MessageService {

    @Autowired
    private MessageMapper messageMapper;

    @Override
    public BasePageVO getPageList(Integer currentPage, Integer pageSize, Map<String, Object> condition) {
        LambdaQueryWrapper<Message> lambdaQueryWrapper = new LambdaQueryWrapper<>();
        BasePageVO<Message> basePageVO = new BasePageVO<>();

        if (condition != null) {
            // 名称模糊查询
            if (condition.get("name") != null) {
                lambdaQueryWrapper.like(Message::getName, condition.get("name"));
            }
            // 未被删除的
            lambdaQueryWrapper.eq(Message::getDeleted, 0);
        }

        // 分页参数构建
        Page<Message> page = messageMapper.selectPage(new Page<>(currentPage, pageSize), lambdaQueryWrapper);
        basePageVO.setPageNum(page.getCurrent());
        basePageVO.setPageSize(page.getSize());
        basePageVO.setTotal(page.getTotal());
        basePageVO.setTotalPage(page.getPages());
        basePageVO.setList(page.getRecords());
        return basePageVO;
    }
}
```

## 七、代码生成器（AutoGenerator）

### 依赖引入

xml

```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-generator</artifactId>
    <version>${mybatisplus.version}</version>
</dependency>
<dependency>
    <groupId>org.apache.velocity</groupId>
    <artifactId>velocity-engine-core</artifactId>
    <version>2.3</version>
</dependency>
```

### 生成器示例代码

java

运行

```java
public class CodeGenerator {
    public static void main(String[] args) {
        // 全局配置
        GlobalConfig gc = new GlobalConfig();
        String projectPath = System.getProperty("user.dir");
        gc.setOutputDir(projectPath + "/src/main/java");
        gc.setAuthor("YourName");
        gc.setOpen(false);
        gc.setServiceName("%sService"); // 去掉Service接口的首字母I
        gc.setIdType(IdType.ASSIGN_ID); // 主键策略

        // 数据源配置
        DataSourceConfig dsc = new DataSourceConfig();
        dsc.setUrl("jdbc:mysql://localhost:3306/your_db?useUnicode=true&characterEncoding=utf8&useSSL=false");
        dsc.setDriverName("com.mysql.cj.jdbc.Driver");
        dsc.setUsername("root");
        dsc.setPassword("root");
        dsc.setDbType(DbType.MYSQL);

        // 包配置
        PackageConfig pc = new PackageConfig();
        pc.setModuleName("yourModule");
        pc.setParent("com.xxx.mp");
        pc.setController("controller");
        pc.setService("service");
        pc.setServiceImpl("service.impl");
        pc.setMapper("mapper");
        pc.setEntity("entity");

        // 策略配置
        StrategyConfig strategy = new StrategyConfig();
        strategy.setInclude("your_table"); // 表名
        strategy.setNaming(NamingStrategy.underline_to_camel);
        strategy.setColumnNaming(NamingStrategy.underline_to_camel);
        strategy.setEntityLombokModel(true);
        strategy.setRestControllerStyle(true);
        strategy.setControllerMappingHyphenStyle(true);
        strategy.setTablePrefix("tb_"); // 表前缀

        // 执行生成
        AutoGenerator mpg = new AutoGenerator();
        mpg.setGlobalConfig(gc);
        mpg.setDataSource(dsc);
        mpg.setPackageInfo(pc);
        mpg.setStrategy(strategy);
        mpg.execute();
    }
}
```

## 八、微服务与技术架构

* **技术架构**：MyBatis-Plus + Docker + 分布式搜索引擎 + 分布式事务 + Redis
* **微服务组件（SpringCloudAlibaba）**：Sentinel（限流降级）、Nacos（服务注册配置）、RocketMQ（消息队列）、Dubbo（RPC 通信）、Seata（分布式事务）、Alibaba Cloud ACM/OSS/ScheduleX/SMS 等
* **功能范围**：增删改查、Excel 导入导出、文件下载上传

## 九、MyBatis-Plus 核心优势

* 对 MyBatis 增强升级，**零侵入性**，一键生成单表 CRUD。
* 提供基于 `BaseMapper`的方法、代码生成、逻辑删除、分页插件等实用功能。
* 单表 CRUD 只需引入依赖，继承 `BaseMapper`并指定泛型为对应实体即可实现。

**依赖引入示例**：

xml

```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>${mybatisplus.version}</version>
</dependency>
```

**属性配置示例**：

xml

```xml
<properties>
    <spring-boot.version>2.2.6.RELEASE</spring-boot.version>
    <mybatisplus.version>3.3.2</mybatisplus.version>
    <!-- 其他依赖版本省略... -->
</properties>
```

## 十、Spring Boot 自动配置原理

MyBatis-Plus 的自动配置通过 `MybatisPlusAutoConfiguration`实现，关键流程：

1. **配置加载**：通过 `spring.factories`文件触发 Spring 扫描加载 `MybatisPlusAutoConfiguration`。
2. **条件注解**：`@Conditional`系列注解（如 `@ConditionalOnClass`、`@ConditionalOnBean`）控制配置生效条件。
3. **配置绑定**：通过 `ConfigurationProperties`将 `application.yml`中的配置绑定到 Java 类（如 `MybatisPlusProperties`）。
4. **自定义覆盖**：用户自定义的配置（如自定义 `SqlSessionFactory`）会覆盖自动配置中的默认属性。
