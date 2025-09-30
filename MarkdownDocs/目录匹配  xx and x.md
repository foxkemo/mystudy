好问题 👍 你问的 `*` 和 `**` 是 **Spring MVC 资源映射**（甚至在 `@RequestMapping` 的路径匹配中）常用的通配符。

------

## 📌 区别解释

### 1. 单星号 `*`

- **匹配一层路径中的任意字符**（不跨目录）
- 不能匹配 `/`

✅ 例子：

```java
registry.addResourceHandler("/img/*")
        .addResourceLocations("classpath:/static/img/");
```

请求路径 → 是否匹配？

- `/img/a.png` ✅
- `/img/abc.jpg` ✅
- `/img/a/b.png` ❌（多了一层目录）

------

### 2. 双星号 `**`

- **匹配多层路径**（可以跨目录）
- 可以匹配 0 个或多个路径层级

✅ 例子：

```java
registry.addResourceHandler("/img/**")
        .addResourceLocations("classpath:/static/img/");
```

请求路径 → 是否匹配？

- `/img/a.png` ✅
- `/img/a/b.png` ✅
- `/img/a/b/c/d.jpg` ✅
- `/img/` ✅

------

## 📌 类比总结

- `*` → “一个文件名”
- `**` → “任意路径层级”

就像文件系统里的：

- `/img/*`  ≈  `/img/某个文件`
- `/img/**` ≈  `/img/下面的所有文件和子目录`

------

要不要我帮你画一个 **路径匹配示意图**（表格形式，`*` vs `**` 直观对比）？