明白了 👍 你想了解 **Web 开发中 Filter 的概念**，我来给你系统梳理。

---

## 1. Filter 的概念

**Filter（过滤器）** 是 **Java Web 规范（Servlet API）提供的一种组件**，用于在 **请求到达 Servlet 或响应返回客户端之前** 对请求/响应进行预处理或后处理。

通俗理解：

> 就像高速路上的“安检口”，每个请求和响应都可以经过过滤器，进行检查、修改或统计，然后再放行或拦截。

---

## 2. Filter 的作用

常见用途包括：

1. **权限校验**：判断用户是否登录，没登录就重定向到登录页。
2. **日志记录**：记录访问日志、请求参数、执行时间等。
3. **请求/响应编码**：统一设置字符编码（如 UTF-8）。
4. **统一处理跨域或安全头**：CORS、CSRF、防 XSS 等。
5. **压缩响应**：GZIP 压缩返回内容，提高性能。

---

## 3. Filter 接口

`javax.servlet.Filter` 是接口，常用方法：

```java
public interface Filter {
    void init(FilterConfig filterConfig) throws ServletException; // 初始化，只执行一次
    void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
        throws IOException, ServletException; // 核心方法，每次请求都会执行
    void destroy(); // 销毁，释放资源
}
```

* **`doFilter()`** 核心：

  * 可以 **操作 request/response**
  * 可以 **决定是否放行**：调用 `chain.doFilter(request, response)` 放行到下一个 Filter 或 Servlet
  * 可以 **在放行前后做处理**（前置/后置操作）

示例：

```java
@WebFilter("/user/*") // 注解方式，过滤 /user/* 请求
public class AuthFilter implements Filter {
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        HttpServletRequest req = (HttpServletRequest) request;
        HttpSession session = req.getSession(false);
        if (session != null && session.getAttribute("user") != null) {
            chain.doFilter(request, response); // 放行
        } else {
            ((HttpServletResponse) response).sendRedirect("/login"); // 拦截
        }
    }
}
```

---

## 4. 配置方式

### （1）注解方式

```java
@WebFilter(urlPatterns = "/user/*")
public class MyFilter implements Filter { ... }
```

### （2）web.xml 配置方式

```xml
<filter>
    <filter-name>MyFilter</filter-name>
    <filter-class>com.example.MyFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>MyFilter</filter-name>
    <url-pattern>/user/*</url-pattern>
</filter-mapping>
```

---

## 5. Filter 执行顺序

* **多个 Filter** 按配置顺序执行：

  * 请求时：按顺序进入过滤器链
  * 响应时：按相反顺序返回

* 核心是 **FilterChain**，决定请求是否继续传递给下一个 Filter 或最终的 Servlet。

---

### 小结

| 概念     | Filter                                       |
| -------- | -------------------------------------------- |
| 位置     | Servlet 规范的一种组件                       |
| 功能     | 请求预处理 / 响应后处理 / 拦截 / 修改        |
| 核心方法 | `doFilter()`                                 |
| 常见用途 | 登录验证、日志、编码设置、安全过滤、响应压缩 |
| 配置方式 | 注解 `@WebFilter` / web.xml                  |

---

如果你想，我可以帮你画一张 **Filter 链执行顺序图**，把请求从浏览器到 Servlet 的流程标出来，包括前置/后置处理，这样理解更直观。

你要我画吗？