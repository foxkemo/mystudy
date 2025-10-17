好的，我们来系统梳理 **Java 反射机制** 以及 **`Class` 类相关常用方法**。

---

## 1. Java 反射机制概念

\*\*反射（Reflection）\*\*是 Java 在运行时动态获取类的信息并操作对象的一种机制。

* 可以获取类的：**类名、方法、字段、构造器、注解等**
* 可以动态：**创建对象、调用方法、访问属性**
* 核心类：`java.lang.Class`、`java.lang.reflect.Method`、`java.lang.reflect.Field`、`java.lang.reflect.Constructor`

**作用**：

1. 动态创建对象（如 Spring IoC）
2. 动态调用方法（如 AOP、动态代理）
3. 访问私有字段或方法
4. 框架开发中广泛使用

---

## 2. 获取 `Class` 对象的方式

假设有一个类：

```java
public class User {
    private String name;
    public int age;

    public User() {}
    public User(String name, int age) { this.name = name; this.age = age; }
    public void sayHello() { System.out.println("Hello, " + name); }
}
```

### 获取 `Class` 对象的几种方式：

```java
// 1. 通过类名.class
Class<User> clazz1 = User.class;

// 2. 通过对象实例.getClass()
User user = new User();
Class<? extends User> clazz2 = user.getClass();

// 3. 通过全限定类名 Class.forName
Class<?> clazz3 = Class.forName("com.example.User");
```

> 注意：`Class` 对象在 JVM 中是 **类的唯一表示**，每个类只对应一个 `Class` 对象。

---

## 3. Class 常用方法

### （1）获取类信息

```java
clazz.getName();         // 完全限定名 com.example.User
clazz.getSimpleName();   // 简单类名 User
clazz.getPackage();      // 包信息
clazz.getSuperclass();   // 父类
clazz.getInterfaces();   // 实现的接口数组
clazz.isInterface();     // 是否是接口
clazz.isEnum();          // 是否是枚举
clazz.isAnnotation();    // 是否是注解
```

### （2）获取构造器

```java
Constructor<?>[] constructors = clazz.getConstructors();         // 公共构造器
Constructor<?>[] allConstructors = clazz.getDeclaredConstructors(); // 所有构造器（含私有）

// 获取特定参数类型的构造器
Constructor<User> cons = clazz.getConstructor(String.class, int.class);
User u = cons.newInstance("Tom", 20);
```

### （3）获取方法

```java
Method[] methods = clazz.getMethods();           // 所有公共方法（包含父类）
Method[] allMethods = clazz.getDeclaredMethods(); // 所有方法（包括私有）

// 获取指定方法
Method m = clazz.getMethod("sayHello");
m.invoke(u);  // 调用实例方法
```

### （4）获取字段

```java
Field[] fields = clazz.getFields();            // 公共字段
Field[] allFields = clazz.getDeclaredFields(); // 所有字段，包括私有

Field ageField = clazz.getField("age");
ageField.set(u, 25);  // 设置公共字段值
System.out.println(ageField.get(u)); // 获取值

Field nameField = clazz.getDeclaredField("name");
nameField.setAccessible(true); // 允许访问私有字段
nameField.set(u, "Jerry");
```

### （5）获取注解

```java
Annotation[] annotations = clazz.getAnnotations(); // 所有运行时注解
MyAnnotation myAnn = clazz.getAnnotation(MyAnnotation.class); // 获取指定注解
```

---

## 4. 小结

**反射的关键点**：

| 特性     | 描述                                                     |
| -------- | -------------------------------------------------------- |
| 动态     | 在运行时操作类、对象、方法、字段                         |
| 核心类   | `Class`、`Method`、`Field`、`Constructor`                |
| 常用用途 | 框架动态创建对象、动态代理、注解解析                     |
| 注意     | 访问私有成员需 `setAccessible(true)`；过度使用会影响性能 |

---

如果你需要，我可以画一张 **Java 反射机制示意图**，展示 **Class → Constructor/Method/Field → 对象实例 → 方法调用** 的流程，直观理解反射操作。

你希望我画吗？