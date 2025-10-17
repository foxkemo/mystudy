在 Spring 中，`@Configuration` 类中 `@Bean` 的创建顺序并不是完全随意的，它有一定的规则和控制机制。下面详细说明：

---

## 1. 默认创建顺序

1. **按依赖顺序**创建

   * Spring 会先解析 `@Bean` 方法之间的依赖关系（方法调用、方法参数注入、`@DependsOn` 注解）
   * 如果 `BeanA` 被 `BeanB` 依赖，Spring 会先创建 `BeanA`，然后创建 `BeanB`。

2. **方法调用顺序无关**

   * 在 `@Configuration` 类中，即使在方法顺序上先定义 `BeanB`，只要它依赖 `BeanA`，Spring 会先创建 `BeanA`。
   * 这是因为 `@Configuration` 类会被 **CGLIB 代理**，通过代理保证方法返回的 Bean 是容器中的实例，而不会重复创建。

---

## 2. 控制 Bean 创建顺序的方法

### （1）方法调用依赖

```java
@Configuration
public class AppConfig {

    @Bean
    public UserService userService() {
        return new UserServiceImpl();
    }

    @Bean
    public OrderService orderService() {
        return new OrderServiceImpl(userService()); // 调用 userService() → Spring 自动保证单例
    }
}
```

* `OrderService` 内部调用了 `userService()` → Spring 会先创建 `UserService`，然后创建 `OrderService`。

---

### （2）方法参数注入

```java
@Bean
public OrderService orderService(UserService userService) {
    return new OrderServiceImpl(userService);
}
```

* Spring 会先解析参数依赖 `UserService` → 创建依赖 Bean → 再创建当前 Bean。
* 这是 **推荐的依赖注入方式**。

---

### （3）使用 `@DependsOn` 注解

* 用于明确声明 Bean 的依赖关系，强制创建顺序：

```java
@Bean
@DependsOn("userService")
public OrderService orderService() { ... }
```

* `userService` 会先被创建。

---

## 3. 单例 Bean 初始化顺序

* **默认是懒加载**还是立即初始化取决于容器：

  * **Singleton Bean**（默认）：

    * 在 **容器启动时**创建，按依赖顺序。
    * `@Lazy` 可延迟到第一次使用。
  * **Prototype Bean**：

    * 每次依赖注入或 `getBean()` 时创建。
* 依赖关系会保证创建顺序，无需手动控制方法顺序。

---

## 4. 总结

| 特性         | 描述                                                        |
| ------------ | ----------------------------------------------------------- |
| 默认顺序     | 按依赖关系创建 Bean，不依赖方法定义顺序                     |
| 方法调用依赖 | 内部调用其他 `@Bean` 方法 → Spring 自动保证依赖 Bean 先创建 |
| 方法参数注入 | Spring 根据参数类型/名称解析依赖 → 先创建依赖 Bean          |
| `@DependsOn` | 显式声明依赖 Bean，强制顺序                                 |
| 单例 vs 多例 | 单例在容器启动时创建（可懒加载），多例在使用时创建          |

> ✅ 核心：**`@Configuration` + CGLIB 代理 + 依赖解析 → Spring 自动保证 Bean 创建顺序**，不必按方法书写顺序。

---

我可以画一张 **@Configuration 类 Bean 创建顺序示意图**，展示 **依赖解析 → 先创建依赖 Bean → 再创建当前 Bean → 注册容器**，非常直观。

你希望我画吗？