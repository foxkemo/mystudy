这个问题要结合 **Spring MVC / Spring Boot 控制器方法参数绑定** 来说。
 在 Controller 的方法参数里，`@RequestBody` 并不是必须的，要不要加取决于 **请求的数据格式**。

------

## 1. **什么时候要加 `@RequestBody`**

- 请求体是 **JSON、XML、或其他非表单格式**，需要反序列化为对象。
- 常见场景：前端用 **Ajax / Axios / Fetch** 以 `application/json` 发送数据。

示例：

```java
@PostMapping("/dish")
public Result addDish(@RequestBody DishDTO dishDTO) {
    // JSON -> DishDTO 自动映射
    return Result.success();
}
```

请求：

```http
POST /dish
Content-Type: application/json

{
  "name": "鱼香肉丝",
  "price": 18.0
}
```

👉 没有 `@RequestBody` 的话，Spring 不会去解析 JSON，而是会认为你是用表单提交（`application/x-www-form-urlencoded`），导致参数绑定失败。

------

## 2. **什么时候不要加 `@RequestBody`**

- 请求是 **表单提交**（`application/x-www-form-urlencoded`）或 **URL 查询参数**。
- 常见场景：浏览器 `<form>` 提交、GET 请求。

示例：

```java
@PostMapping("/dish")
public Result addDish(DishDTO dishDTO) {
    // 表单的 key=value 自动绑定到 DishDTO
    return Result.success();
}
```

请求：

```http
POST /dish
Content-Type: application/x-www-form-urlencoded

name=鱼香肉丝&price=18.0
```

------

## 3. **总结对比**

| 请求方式 / 数据格式                                  | 是否需要 `@RequestBody` | 示例                  |
| ---------------------------------------------------- | ----------------------- | --------------------- |
| GET 请求，参数在 URL query string                    | ❌ 不要                  | `/dish?id=1&status=1` |
| POST 表单提交（`application/x-www-form-urlencoded`） | ❌ 不要                  | `name=xx&price=xx`    |
| POST JSON 请求（`application/json`）                 | ✅ 需要                  | `{ "name": "xx" }`    |
| POST 文件上传（`multipart/form-data`）               | ❌ 不要                  | `MultipartFile file`  |

------

⚡ 记忆口诀：

- **前端 JSON → 后端要加 `@RequestBody`**
- **前端表单 / query 参数 → 不要加 `@RequestBody`**

------

要不要我帮你整理一个 **Spring Controller 参数绑定速查表**（包含 `@RequestBody`、`@RequestParam`、`@PathVariable`、`@RequestHeader`）？这样你以后就能一眼分辨该用哪个。