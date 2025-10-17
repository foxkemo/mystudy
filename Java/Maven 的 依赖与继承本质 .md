这个问题问得好 👍。
答案是：**不一定**。

---

## 1. parent 是什么

在 Maven 里，`<parent>` 指的是**父 POM**，它本身也是一个 Maven 项目。
但是这个父 POM **不一定是一个 Java 包（Jar/War）**，它也可能是：

* **仅配置用的父 POM**

  * `packaging` = `pom`
  * 不写 Java 源代码、不打包成 Jar，只用来放公共配置（依赖版本、插件、属性）。
  * 常见于企业、团队的“项目骨架（parent POM）”。

* **普通的 Java 模块**

  * `packaging` = `jar` 或 `war`
  * 既是父 POM，又能打包成 Java 包，被其他项目依赖。
  * 这种情况比较少见，但也有，比如 Spring Boot 的 `spring-boot-starter-parent`，它虽然能作为父 POM，但本质上也会打一个 Jar 包。

---

## 2. 举例说明

### (1) 配置型 parent（纯粹做配置继承）

```xml
<project>
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>parent-project</artifactId>
    <version>1.0.0</version>
    <packaging>pom</packaging>
</project>
```

👉 这种父项目 **不产生 Java 包**，只是配置容器。

---

### (2) 代码型 parent（同时是 Java 包）

比如 Spring Boot 官方的 `spring-boot-starter-parent`：

```xml
<project>
    <modelVersion>4.0.0</modelVersion>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.2.0</version>
    <packaging>pom</packaging>
</project>
```

它也是 `packaging=pom`，但内部依赖了 `spring-boot-dependencies` 来做依赖管理，本身没有源代码，不打 Jar。

另一个例子：你完全可以写一个普通的 `jar` 项目，里面有代码，同时让别的项目 `<parent>` 继承它，这样它既是一个可用的依赖，又是一个父 POM。

---

## 3. 总结

* **parent 本质上是一个 Maven 项目**。
* 是否是 Java 包，取决于它的 `<packaging>`：

  * `pom` → 纯配置，不是 Java 包。
  * `jar`/`war` → 是 Java 包，可以作为依赖使用。
* 但在企业里，**大多数 parent 项目都是 `pom` 类型**，不包含 Java 代码。

---

要不要我帮你对比下 **parent 和 dependency 的区别**？很多人容易把“继承 parent”和“依赖 dependency”混淆。