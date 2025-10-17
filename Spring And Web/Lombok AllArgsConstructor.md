`@AllArgsConstructor` 是 **Lombok** 提供的一个注解，不是 Spring 自带的。它的作用很简单：

👉 **自动为类生成一个包含所有字段的构造函数**。

------

## 1. 基本用法

```java
import lombok.AllArgsConstructor;

@AllArgsConstructor
public class User {
    private String name;
    private int age;
}
```

等价于你手写：

```java
public class User {
    private String name;
    private int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

------

## 2. 常见配合场景

### （1）配合 `@Autowired` 做构造器注入

在 Spring 里常见这种写法：

```java
@Service
@AllArgsConstructor
public class UserService {
    private final UserRepository userRepository;
    private final EmailService emailService;
}
```

等价于：

```java
@Service
public class UserService {
    private final UserRepository userRepository;
    private final EmailService emailService;

    @Autowired
    public UserService(UserRepository userRepository, EmailService emailService) {
        this.userRepository = userRepository;
        this.emailService = emailService;
    }
}
```

因为 Spring **推荐构造器注入**，而 Lombok 自动生成构造器 → 代码更简洁。

------

### （2）和 `@NoArgsConstructor`、`@RequiredArgsConstructor` 对比

- `@NoArgsConstructor` → 无参构造器
- `@RequiredArgsConstructor` → 只生成 `final` 或 `@NonNull` 字段的构造器
- `@AllArgsConstructor` → 生成包含所有字段的构造器

------

## 3. 注意点

- 如果类里已经手写了构造器，Lombok **不会重复生成**。

- 默认生成的构造器是 `public`，你可以通过 `@AllArgsConstructor(access = AccessLevel.PRIVATE)` 控制访问级别。

- 在和 JPA/Hibernate 一起用时，常见搭配是：

  ```java
  @Entity
  @NoArgsConstructor
  @AllArgsConstructor
  public class User { ... }
  ```

  因为 JPA 要求有无参构造器。

------

要不要我帮你整理一份 **`@AllArgsConstructor` 和 `@RequiredArgsConstructor` 在 Spring 项目里构造器注入的最佳实践对比**？