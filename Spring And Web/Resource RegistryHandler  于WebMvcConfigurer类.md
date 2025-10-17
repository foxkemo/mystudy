`ResourceHandlerRegistry` 是 **Spring MVC** 提供的一个类，用来在配置类中手动注册静态资源的访问规则。它常常和 `WebMvcConfigurer` 接口一起使用，通过重写 `addResourceHandlers()` 方法配置。

------

## 📌 基本用法

```java
@Configuration
public class WebMvcConfig implements WebMvcConfigurer {

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        // 将 /static/** 的请求映射到 classpath:/static/ 目录
        registry.addResourceHandler("/static/**")
                .addResourceLocations("classpath:/static/");

        // 将 /images/** 的请求映射到本地磁盘路径
        registry.addResourceHandler("/images/**")
                .addResourceLocations("file:/opt/images/");

        // 将 /docs/** 的请求映射到 META-INF/resources
        registry.addResourceHandler("/docs/**")
                .addResourceLocations("classpath:/META-INF/resources/");
    }
}
```

------

## 📌 方法说明

- `addResourceHandler("/pattern/**")`
   指定匹配的 **URL 路径规则**（请求路径）。支持 `*` 和 `**` 通配符。
   例如：`/static/**` 表示匹配 `/static/` 下的所有请求。
- `addResourceLocations("location...")`
   指定静态资源的 **物理存放位置**，支持多个路径。常见形式：
  - `classpath:/目录/` → 项目 `resources` 下的目录
  - `file:/本地路径/` → 本地磁盘目录

------

## 📌 应用场景

1. **静态资源访问**
   - 比如 `/images/**` 映射到磁盘路径 `/opt/images/`
   - 常用于上传文件后，提供外部访问接口
2. **Swagger UI**
   - Swagger 的 `doc.html` 和 `/webjars/**` 就是通过 `ResourceHandlerRegistry` 配置的
3. **前后端分离部署**
   - 将前端打包好的静态文件放在 Spring Boot 的 `classpath:/static/` 里，然后通过映射对外提供

------

## 📌 默认行为

Spring Boot 默认会自动映射以下路径到对应目录（不需要手动写）：

- `/webjars/**` → `classpath:/META-INF/resources/webjars/`
- `/**` → `classpath:/META-INF/resources/`, `classpath:/resources/`, `classpath:/static/`, `classpath:/public/`

👉 所以很多情况下你不写 `addResourceHandlers`，也能访问 `static`、`public` 目录下的文件。

------

要不要我帮你写一个 **上传图片到本地磁盘并通过 URL 访问** 的 `ResourceHandlerRegistry` 配置示例？这样比 Swagger 配置更直观。