# MyBatis-Plus 核心笔记（完整版）

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

* 支持链式调用，通过方法引用直接关联实体字段，避免字符串拼写错误。
* 可组合 `and`、`or`、`in`、`like` 等多种条件。

### Lambda 更新

java

运行

```java
LambdaUpdateWrapper<UserExam> lambdaUpdate = new LambdaUpdateWrapper<>();
lambdaUpdate.set(UserExam::getRemark, remark).update();
```

* 精准更新指定字段，自动拼接 `WHERE` 条件。

## 二、批量插入优化

* **配置参数**：`rewriteBatchedStatements = true`（需在 JDBC 连接串中开启，如 `jdbc:mysql://...?rewriteBatchedStatements=true`），可将多条插入 SQL 合并为批量操作，大幅提升性能。
* **对比方案**：
  * 普通 for 循环逐条插入：性能极差，每条 SQL 都需建立连接，不推荐。
  * IService 批量插入：`userService.saveBatch(userList, 1000)`（第二个参数为批量提交的批次大小）。
  * 开启 `rewriteBatchedStatements` 参数：结合 IService 批量插入，性能最优。

## 三、逻辑删除配置

在 `application.yml` 中配置全局逻辑删除规则：

yaml

```yaml
mybatis-plus:
  global-config:
    db-config:
      logic-delete-field: del_flag  # 全局逻辑删除的字段名，支持 boolean、integer 类型
      logic-delete-value: 1         # 逻辑已删除值（默认 1）
      logic-not-delete-value: 0     # 逻辑未删除值（默认 0）
```

* 执行 `deleteById` 时，自动转换为 `UPDATE 表 SET del_flag=1 WHERE id=?`。
* 查询时自动追加 `WHERE del_flag=0`，无需手动过滤。

## 四、通用枚举配置

在 `application.yml` 中配置全局枚举处理器：

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

    @EnumValue  // 标记数据库存储的枚举值
    private final int value;
    private final String desc;

    UserStatus(int value, String desc) {
        this.value = value;
        this.desc = desc;
    }
}
```

* 枚举与数据库字段自动映射，无需手动转换。

## 五、核心注解与配置

### 主键生成策略

java

运行

```java
// 数据库自增（需数据库表主键设置为 AUTO_INCREMENT）
@TableId(value = "id", type = IdType.AUTO)

// 手动输入（需业务层自行设置 id）
@TableId(value = "id", type = IdType.INPUT)

// 雪花算法生成（分布式唯一 ID，推荐）
@TableId(value = "id", type = IdType.ASSIGN_ID)

// 生成 UUID（字符串类型主键）
@TableId(value = "id", type = IdType.ASSIGN_UUID)
```

### 字段与表映射

java

运行

```java
// 显式映射数据库字段（当实体字段与数据库字段命名不一致时）
@TableField("blue_subsystem_id")

// 映射数据库表名（当实体类名与表名不一致时）
@TableName("blue_payload_setting")
```

### MyBatis-Plus 全局配置

yaml

```yaml
mybatis-plus:
  type-aliases-package: com.xxx.mp.domain.po  # 实体类别名扫描包，简化 XML 中类型引用
  mapper-locations: "classpath:/mapper/**/*.xml"  # 自定义 Mapper XML 文件路径
  configuration:
    map-underscore-to-camel-case: true  # 开启驼峰命名自动映射（如 db_field → entityField）
    cache-enabled: true  # 开启二级缓存
  global-config:
    db-config:
      id-type: auto  # 全局主键策略（默认 AUTO）
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

    // Getter 和 Setter 方法省略...
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
            // 过滤未删除数据
            lambdaQueryWrapper.eq(Message::getDeleted, 0);
        }

        // 分页查询
        Page<Message> page = messageMapper.selectPage(
            new Page<>(currentPage, pageSize), 
            lambdaQueryWrapper
        );

        // 封装分页结果
        basePageVO.setPageNum(page.getCurrent());
        basePageVO.setPageSize(page.getSize());
        basePageVO.setTotal(page.getTotal());
        basePageVO.setTotalPage(page.getPages());
        basePageVO.setList(page.getRecords());
        return basePageVO;
    }
}
```

* 需先配置分页插件（见下文 “配置扩展”）。

## 七、代码生成器（AutoGenerator）

### 依赖引入

xml

```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-generator</artifactId>
    <version>3.5.3.2</version>
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
        gc.setOutputDir(projectPath + "/src/main/java"); // 生成文件的输出目录
        gc.setAuthor("YourName"); // 作者
        gc.setOpen(false); // 生成后是否打开文件夹
        gc.setServiceName("%sService"); // 去掉 Service 接口的首字母 I
        gc.setIdType(IdType.ASSIGN_ID); // 主键策略（雪花算法）

        // 数据源配置
        DataSourceConfig dsc = new DataSourceConfig();
        dsc.setUrl("jdbc:mysql://localhost:3306/your_db?useUnicode=true&characterEncoding=utf8&useSSL=false");
        dsc.setDriverName("com.mysql.cj.jdbc.Driver");
        dsc.setUsername("root");
        dsc.setPassword("root");
        dsc.setDbType(DbType.MYSQL);

        // 包配置
        PackageConfig pc = new PackageConfig();
        pc.setModuleName("yourModule"); // 模块名
        pc.setParent("com.xxx.mp"); // 父包名
        pc.setController("controller");
        pc.setService("service");
        pc.setServiceImpl("service.impl");
        pc.setMapper("mapper");
        pc.setEntity("entity");

        // 策略配置
        StrategyConfig strategy = new StrategyConfig();
        strategy.setInclude("your_table", "another_table"); // 要生成的表名（可变参数）
        strategy.setNaming(NamingStrategy.underline_to_camel); // 表名转驼峰命名
        strategy.setColumnNaming(NamingStrategy.underline_to_camel); // 字段名转驼峰命名
        strategy.setEntityLombokModel(true); // 实体类使用 Lombok 注解
        strategy.setRestControllerStyle(true); // 生成 @RestController 控制器
        strategy.setControllerMappingHyphenStyle(true); // 控制器路由使用连字符（如 /user-list）
        strategy.setTablePrefix("tb_"); // 过滤表前缀（如 tb_user → User）

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

* 可一键生成 Entity、Mapper、Service、Controller 及对应的 XML 文件。

## 八、微服务与技术架构

* **技术架构**：MyBatis-Plus + Docker + 分布式搜索引擎（如 Elasticsearch） + 分布式事务（如 Seata） + Redis。
* **微服务组件（SpringCloudAlibaba 生态）**：
  * Sentinel：流量控制、熔断降级。
  * Nacos：服务注册与配置中心。
  * RocketMQ：分布式消息队列。
  * Dubbo：高性能 RPC 通信框架。
  * Seata：分布式事务解决方案。
  * Alibaba Cloud 组件：ACM（配置管理）、OSS（对象存储）、ScheduleX（定时任务）、SMS（短信服务）等。
* **功能范围**：
  * 基础 CRUD 操作。
  * Excel 导入导出（可结合 EasyExcel 实现）。
  * 文件下载与上传（结合 OSS 或本地存储）。

## 九、MyBatis-Plus 核心优势

* **零侵入增强**：基于 MyBatis 扩展，不修改原有 MyBatis 逻辑，兼容所有 MyBatis 特性。
* **单表 CRUD 自动化**：继承 `BaseMapper` 或 `IService` 即可获得 17 种单表操作方法，无需手写 SQL。
* **丰富功能插件**：内置分页、逻辑删除、性能分析、乐观锁等插件，开箱即用。
* **Lambda 表达式支持**：通过方法引用实现类型安全的条件构造，避免字符串硬编码。

**依赖引入示例**：

xml

```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.5.3.2</version>
</dependency>
```

**版本属性配置示例**：

xml

```xml
<properties>
    <spring-boot.version>2.7.10</spring-boot.version>
    <mybatisplus.version>3.5.3.2</mybatisplus.version>
    <!-- 其他依赖版本省略... -->
</properties>
```

## 十、Spring Boot 自动配置原理

MyBatis-Plus 的自动配置由 `MybatisPlusAutoConfiguration` 类实现，核心流程：

1. **配置加载**：通过 `META-INF/spring.factories` 文件，Spring 会自动扫描并加载 `MybatisPlusAutoConfiguration`。
2. **条件注解控制**：
   * `@ConditionalOnClass`：当类路径存在 `MybatisPlus` 核心类时，配置生效。
   * `@ConditionalOnBean`：当容器中存在指定 Bean 时，配置生效。
3. **配置绑定**：通过 `@ConfigurationProperties(prefix = "mybatis-plus")` 将 `application.yml` 中的配置绑定到 `MybatisPlusProperties` 类。
4. **自定义覆盖**：若用户自定义了 `SqlSessionFactory`、`MapperScannerConfigurer` 等 Bean，自动配置会优先使用用户自定义的配置。

## 十一、配置扩展（插件与拦截器）

### 分页插件配置

java

运行

```java
@Configuration
public class MyBatisPlusConfig {
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
        // 分页插件（支持 MySQL、Oracle、PostgreSQL 等）
        interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.MYSQL));
        return interceptor;
    }
}
```

### 乐观锁插件

java

运行

```java
@Configuration
public class MyBatisPlusConfig {
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
        // 乐观锁插件（用于解决并发更新冲突）
        interceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
        return interceptor;
    }
}

// 实体类中标记乐观锁字段
public class Product {
    @Version
    private Integer version;
    // 其他字段...
}
```

### 性能分析插件（开发环境用）

java

运行

```java
@Configuration
public class MyBatisPlusConfig {
    @Bean
    @Profile({"dev", "test"}) // 仅在开发/测试环境生效
    public PerformanceInterceptor performanceInterceptor() {
        PerformanceInterceptor interceptor = new PerformanceInterceptor();
        interceptor.setMaxTime(1000); // SQL 执行超时时间（毫秒）
        interceptor.setFormat(true); // SQL 格式化输出
        return interceptor;
    }
}
```
