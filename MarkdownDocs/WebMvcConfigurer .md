好问题 👍
 `WebMvcConfigurer` 是 Spring MVC 提供的一个 **配置扩展接口**，我们可以通过实现它，来自定义 Spring MVC 的各种行为。

它本身是一个 **接口（interface）**，里面大部分方法都是 **default 方法**，所以我们只需要重写需要的部分即可。

------

## 📌 常用方法（分类记忆）

### 1. 静态资源映射

```java
void addResourceHandlers(ResourceHandlerRegistry registry);
```

👉 配置静态资源路径（你刚才的 `doc.html` 和 `webjars/**` 就在这里）。

------

### 2. 跨域配置

```java
void addCorsMappings(CorsRegistry registry);
```

👉 配置全局 CORS 跨域规则。

------

### 3. 拦截器配置

```java
void addInterceptors(InterceptorRegistry registry);
```

👉 注册自定义拦截器（比如登录校验拦截器）。

------

### 4. 视图控制器（不写 Controller 直接跳转）

```java
void addViewControllers(ViewControllerRegistry registry);
```

👉 访问某个 URL 直接跳转到视图，比如首页 `/` → `index.html`。

------

### 5. 消息转换器（HTTP 消息 <-> Java 对象）

```java
void configureMessageConverters(List<HttpMessageConverter<?>> converters);
void extendMessageConverters(List<HttpMessageConverter<?>> converters);
```

👉 配置 JSON、XML 转换方式（Jackson、Gson 等）。

------

### 6. 异常处理器

```java
void configureHandlerExceptionResolvers(List<HandlerExceptionResolver> resolvers);
void extendHandlerExceptionResolvers(List<HandlerExceptionResolver> resolvers);
```

👉 自定义全局异常解析（比如统一返回 JSON 错误格式）。

------

### 7. 内容协商（决定返回 JSON 还是 XML）

```java
void configureContentNegotiation(ContentNegotiationConfigurer configurer);
```

------

### 8. 视图解析器

```java
void configureViewResolvers(ViewResolverRegistry registry);
```

👉 配置 JSP、Thymeleaf 视图解析。

------

### 9. 参数解析器 & 返回值处理器

```java
void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers);
void addReturnValueHandlers(List<HandlerMethodReturnValueHandler> handlers);
```

👉 自定义 Controller 方法的参数绑定与返回值处理。

------

### 🔟 异步支持

```java
void configureAsyncSupport(AsyncSupportConfigurer configurer);
```

👉 配置异步请求（超时时间、线程池等）。

------

## 📌 `WebMvcConfigurer` 的 field？

其实 **`WebMvcConfigurer` 接口本身没有字段（field）**，因为它是接口，只有方法（大部分是 `default`）。
 但是我们常在实现类里定义 **自定义的 bean、拦截器、常量** 等作为字段。

------

## 📌 常见实现示例

```java
@Configuration
public class MyWebMvcConfig implements WebMvcConfigurer {

    // 例子：添加静态资源路径
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/static/**")
                .addResourceLocations("classpath:/static/");
    }

    // 例子：配置跨域
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")
                .allowedOrigins("*")
                .allowedMethods("GET","POST","PUT","DELETE");
    }

    // 例子：注册拦截器
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginInterceptor())
                .addPathPatterns("/**")
                .excludePathPatterns("/login", "/static/**");
    }
}
```

------

要不要我帮你整理一个 **WebMvcConfigurer 方法速查表（表格版：方法名 | 作用 | 常见用法）**？这样比一大段文字更直观。

好 👍 你问的 `WebMvcConfigurer` 在 **Spring 框架中的位置**，可以从几个层面来理解：

------

## 1. 所属包

- **接口位置**：

  ```java
  package org.springframework.web.servlet.config.annotation;
  ```

  所以它是 **Spring MVC**（web 层框架）的一个配置扩展接口。

------

## 2. 作用定位

Spring MVC 的工作核心是 **DispatcherServlet**（前端控制器），它负责把请求转发给 Controller，然后返回视图或 JSON。
 而 `WebMvcConfigurer` 处在 **配置扩展层**，允许我们在 **默认的 MVC 配置基础上做定制化**。

👉 关系图：

```
用户请求
   ↓
DispatcherServlet （前端控制器）
   ↓
HandlerMapping / HandlerAdapter / ViewResolver （MVC 核心组件）
   ↓
Controller 业务逻辑
   ↓
返回 ModelAndView 或 ResponseBody
```

在这个流程中，`WebMvcConfigurer` 能够插入点有：

- 配置 **静态资源映射**（不经过 Controller，直接返回文件）
- 配置 **拦截器链**（请求到 Controller 之前）
- 配置 **消息转换器**（Controller ↔ JSON/XML）
- 配置 **异常处理器**（Controller 抛异常时）
- 配置 **视图解析器**（Controller 返回逻辑视图名时）

------

## 3. 与其他配置类的关系

- `WebMvcConfigurer` 是一个 **轻量接口**，我们只需实现需要的方法。
- `@EnableWebMvc` → 启用 **完全手动配置**，替代 Spring Boot 默认的自动配置。
- 在 Spring Boot 中：
  - **默认静态资源映射**等由 `WebMvcAutoConfiguration` 提供。
  - 如果我们实现了 `WebMvcConfigurer`，Spring 会 **合并配置**（不是覆盖），所以常用。
  - 如果使用 `@EnableWebMvc`，则关闭 Boot 的默认 MVC 配置，需要完全自己配置。

------

## 4. 典型层级关系（简化 UML）

```
WebMvcConfigurer (接口)
    ↑
MyWebMvcConfig (你写的配置类，@Configuration)
    ↓
被 Spring Boot 自动加载 → 影响 DispatcherServlet 行为
```

------

✅ 简单一句话总结：
 **`WebMvcConfigurer` 就是 Spring MVC 提供的一个扩展接口，位于配置层，用来在默认 MVC 行为上进行自定义，而不会打破 Spring Boot 提供的自动配置。**

------

要不要我帮你画一个 **Spring MVC 请求处理流程图（标出 WebMvcConfigurer 的切入点）**，这样你能更清楚它在框架里的“位置”？