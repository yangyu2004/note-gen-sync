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
  # 驼峰转换、二级缓存、id生成类型、更新策略（not null 只更新非空字段）等配置
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

    // 构造方法与getter/setter省略...

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

## 七、MyBatis-Plus 核心优势

* 对 MyBatis 增强升级，零侵入性，一键生成单表 CRUD。
* 提供基于 `BaseMapper`的方法、代码生成、逻辑删除等实用功能。
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
