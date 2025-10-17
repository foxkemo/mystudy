对，你理解得完全正确，我来系统说明一下这个特性及原理：

---

## 1. `@Configuration` 类的作用

* `@Configuration` 标记的类是 **Spring 配置类**，相当于 XML 配置文件。
* 它告诉 Spring **该类中定义的 `@Bean` 方法要被解析，并将返回对象注册为 IoC 容器中的 Bean**。

> 核心特点：**自动初始化 Bean 并放入容器**，不需要手动 `new` 对象。

---

## 2. 工作原理

### （1）CGLIB 代理

* Spring 会对 `@Configuration` 类生成 **CGLIB 子类代理**
* 代理逻辑：

  * 当你调用 `@Bean` 方法时，Spring 会先检查 IoC 容器中是否已存在对应 Bean
  * 如果存在 → 返回已有 Bean
  * 如果不存在 → 调用方法创建 Bean 并放入容器

### （2）保证单例

示例：

```java
@Configuration
public class AppConfig {

    @Bean
    public UserService userService() {
        System.out.println("创建 UserService");
        return new UserServiceImpl();
    }

    @Bean
    public OrderService orderService() {
        return new OrderServiceImpl(userService());
    }
}
```

**运行效果**：

```
创建 UserService
```

* 即使 `orderService()` 内部调用了 `userService()` 方法，Spring **不会重复创建 UserService**，保证单例。

> 如果去掉 `@Configuration`，只是 `@Bean` 的普通类，调用 `userService()` 会每次返回一个新实例，不保证单例。

---

## 3. IoC 容器中的初始化

* Spring **启动时**会扫描配置类
* 找到 `@Bean` 注解的方法
* 调用方法 → 将返回对象注册到 **ApplicationContext**
* 可通过依赖注入直接获取 Bean

```java
ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
UserService userService = context.getBean(UserService.class);
```

* 不需要手动 `new UserServiceImpl()`，完全由容器管理。

---

## 4. 总结

| 特性         | 说明                                                     |
| ------------ | -------------------------------------------------------- |
| 自动初始化   | Spring 在容器启动时调用 `@Bean` 方法创建 Bean            |
| 容器管理     | 返回对象被注册到 IoC 容器，可依赖注入                    |
| 单例保证     | `@Configuration` + CGLIB 代理，方法多次调用返回同一 Bean |
| 生命周期控制 | 可通过 `initMethod` / `destroyMethod` 控制初始化和销毁   |

✅ **关键点**：`@Configuration` 类不仅是普通类，它在 Spring 容器中会被 **代理增强**，保证 `@Bean` 方法返回值 **自动初始化并加入 IoC 容器**，同时支持依赖注入和单例保证。

---

如果你希望，我可以画一张 **@Configuration + @Bean Bean 初始化和单例保证流程图**，展示 **方法调用 → CGLIB 代理 → 容器检查 → 返回 Bean** 的全过程。

你希望我画吗？