PageHelper 是 MyBatis 生态里常用的 **分页插件**，通过拦截 SQL 查询语句自动添加分页语法（`limit/offset`），避免开发者手写分页逻辑。常见于传统项目和 Spring/Spring Boot + MyBatis 的结合中。下面给你整理一下核心用法：

------

## 1. 引入依赖

如果你用的是 **Maven**：

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.4.7</version>
</dependency>
```

> Spring Boot 推荐用 `pagehelper-spring-boot-starter`。
>  普通 Spring/MyBatis 项目可用 `pagehelper` 依赖并在 MyBatis 配置中注册插件。

------

## 2. 基本用法

核心类：`PageHelper`、`PageInfo`。

### 分页查询示例

```java
import com.github.pagehelper.PageHelper;
import com.github.pagehelper.PageInfo;

@Service
public class UserService {
    @Autowired
    private UserMapper userMapper;

    public PageInfo<User> getUserList(int pageNum, int pageSize) {
        // 设置分页参数，下一条查询会自动附加 limit/offset
        PageHelper.startPage(pageNum, pageSize);
        
        // 查询数据（必须是紧跟的第一个 select 语句）
        List<User> users = userMapper.selectAll();
        
        // 封装 PageInfo，包含分页信息（总数、页码、总页数等）
        return new PageInfo<>(users);
    }
}
```

### Mapper 层

```java
@Mapper
public interface UserMapper {
    @Select("SELECT * FROM user")
    List<User> selectAll();
}
```

------

## 3. PageInfo 结果

`PageInfo` 会封装分页元信息，比如：

```java
PageInfo<User> pageInfo = userService.getUserList(1, 10);

pageInfo.getList();       // 当前页的数据
pageInfo.getTotal();      // 总记录数
pageInfo.getPages();      // 总页数
pageInfo.getPageNum();    // 当前页码
pageInfo.getPageSize();   // 每页大小
pageInfo.isHasNextPage(); // 是否有下一页
```

------

## 4. 常用配置

在 `application.yml` 里可以配置 PageHelper：

```yaml
pagehelper:
  helperDialect: mysql   # 方言（支持 mysql, oracle, postgresql, etc.）
  reasonable: true       # 页码合理化：<=0 自动设为1，>最大页自动设为最后一页
  supportMethodsArguments: true  # 支持从方法参数中获取分页参数
  params: count=countSql
```

------

## 5. 注意事项

- `PageHelper.startPage()` 必须紧跟着 **一次 select 查询**，才能生效。
- 常见坑：如果中间调用了别的方法导致执行多个查询，分页可能失效。
- **MyBatis-Plus** 本身也有分页插件，不要和 PageHelper 混用。
- `PageInfo` 可以直接传给前端（如 Vue/React），前端能直接获取总数、页码等。

------

要不要我帮你画一张 **PageHelper 工作原理的流程图**（拦截器插入 SQL → 执行分页 → 封装 PageInfo）？这样能和你之前的 Spring Web 流程图呼应起来。