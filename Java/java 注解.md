**好的，我们系统整理一下 **Java 注解（Annotation）相关知识**，涵盖概念、分类、作用、使用及进阶知识。

---

## 1. 注解的概念

\*\*注解（Annotation）\*\*是 Java 5 引入的一种 **元数据机制**，可以给类、方法、字段、参数等元素提供额外信息，**不直接改变程序逻辑**，但可以被编译器或运行时工具解析处理。

---

## 2. 注解的作用

1. **编译时检查**

   * 通过注解可以在编译期对代码做检查，例如：

   ```java
   @Override
   public String toString() { ... }
   ```

2. **生成文档**

   * 如 `@Deprecated` 可以生成文档提示该方法已过期。

3. **运行时处理（反射）**

   * Spring、JUnit、Hibernate 等框架利用注解在运行时实现功能。

4. **代码自动生成/配置**

   * Lombok、MapStruct 等利用注解生成 getter/setter、映射代码。

---

## 3. 注解的分类

### （1）按作用范围

| 类型                      | 描述                                                         |
| ------------------------- | ------------------------------------------------------------ |
| **源注解（SOURCE）**      | 编译后即丢弃，不保留在字节码中，如 `@Override`               |
| **类注解（CLASS）**       | 编译时保留在字节码中，但运行时不可见（默认）                 |
| **运行时注解（RUNTIME）** | 编译后保留在字节码中，运行时可以通过反射读取，如 Spring 注解 |

* 定义注解保留策略：

```java
@Retention(RetentionPolicy.RUNTIME)
public @interface MyAnnotation { ... }
```

### （2）按用途分类

1. **标记注解（Marker Annotation）**

   * 没有成员，用来标记信息
   * 示例：`@Override`、`@Deprecated`

2. **单值注解（Single-Value Annotation）**

   * 只有一个成员，且可以用 `value` 简写

   ```java
   @SuppressWarnings("unchecked")
   ```

3. **完整注解（Full Annotation）**

   * 可以有多个成员，需显式赋值

   ```java
   @MyAnnotation(name="Tom", age=18)
   ```

---

## 4. 注解的元注解

Java 提供 **元注解** 来修饰注解本身：

| 元注解        | 作用                                               |
| ------------- | -------------------------------------------------- |
| `@Target`     | 指定注解可以用在哪些元素（类、方法、字段、参数等） |
| `@Retention`  | 指定注解生命周期（SOURCE、CLASS、RUNTIME）         |
| `@Documented` | 注解出现在 Javadoc 文档中                          |
| `@Inherited`  | 子类会继承父类的注解                               |

示例：

```java
@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
public @interface MyAnnotation { ... }
```

---

## 5. 注解使用示例

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface LogExecution {
    String value() default "";
}

// 使用注解
public class UserService {
    @LogExecution("添加用户操作")
    public void addUser(String name) {
        System.out.println("添加用户: " + name);
    }
}

// 通过反射解析注解
Method method = UserService.class.getMethod("addUser", String.class);
if (method.isAnnotationPresent(LogExecution.class)) {
    LogExecution log = method.getAnnotation(LogExecution.class);
    System.out.println("注解值: " + log.value());
}
```

---

## 6. 可重复注解（Repeatable）

* Java 8 新特性，可以在同一个元素上多次使用同一个注解：

```java
@Repeatable(Schedules.class)
public @interface Schedule {
    String dayOfWeek();
}

public @interface Schedules {
    Schedule[] value();
}

// 使用
@Schedule(dayOfWeek="Mon")
@Schedule(dayOfWeek="Tue")
public void work() { ... }
```

---

## 7. 框架中常用注解

| 框架    | 注解示例                                                 | 用途                 |
| ------- | -------------------------------------------------------- | -------------------- |
| Java SE | `@Override`, `@Deprecated`, `@SuppressWarnings`          | 编译检查、文档       |
| Spring  | `@Autowired`, `@Component`, `@Service`, `@Transactional` | 依赖注入、声明式事务 |
| JPA     | `@Entity`, `@Id`, `@Column`                              | ORM 映射数据库表     |
| JUnit   | `@Test`, `@Before`, `@After`                             | 测试方法标记         |
| Lombok  | `@Getter`, `@Setter`, `@Data`                            | 自动生成代码         |

---

## 8. 总结

1. 注解是 **元数据机制**，本身不改变逻辑
2. 注解通过 **编译期检查、运行时反射、代码生成** 实现不同功能
3. 核心知识点：**元注解、Retention、Target、可重复注解、反射获取注解**
4. 框架广泛使用注解实现 AOP、依赖注入、事务、ORM 等

---

如果你需要，我可以帮你画一张 **Java 注解分类与使用流程图**，把 **元注解 → 自定义注解 → 运行时解析 → 框架应用** 全部展示出来，更直观。

你希望我画吗？**