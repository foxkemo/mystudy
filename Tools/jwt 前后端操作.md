jwt 令牌 前后端操作

好的 👍 我来帮你系统梳理一下 **JWT（JSON Web Token）令牌在前后端的操作流程**。

---

## 1. JWT 基础概念

* **JWT** 是一种无状态认证机制，用于在前后端分离的系统中传递用户身份。
* **结构**（三部分，用 `.` 分隔）：

  ```
  header.payload.signature
  ```

  * **Header**：说明加密算法和类型（通常是 HS256）。
  * **Payload**：用户数据（如 `userId`, `roles`），是 Base64URL 编码。
  * **Signature**：签名，用于验证数据是否被篡改。

例如：

```
eyJhbGciOiJIUzI1NiJ9.eyJpZCI6MSwidXNlcm5hbWUiOiJhZG1pbiJ9.xxxxxxx
```

---

## 2. JWT 在后端的操作

### （1）生成 JWT

后端在 **用户登录成功后** 生成一个 JWT，返回给前端。

示例（Java, 使用 `io.jsonwebtoken` 库 JJWT）：

```java
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import java.util.Date;

public class JwtUtil {
    private static final String SECRET = "mySecretKey"; // 签名密钥

    public static String generateToken(Long userId, String username) {
        return Jwts.builder()
                .setSubject(username) // 主题，一般存用户名
                .claim("userId", userId) // 自定义负载
                .setIssuedAt(new Date()) // 签发时间
                .setExpiration(new Date(System.currentTimeMillis() + 3600_000)) // 过期时间 1小时
                .signWith(SignatureAlgorithm.HS256, SECRET) // 签名算法+密钥
                .compact();
    }
}
```

### （2）验证 JWT

后端在 **需要鉴权的接口** 中解析 JWT：

```java
public static Claims parseToken(String token) {
    return Jwts.parser()
            .setSigningKey(SECRET)
            .parseClaimsJws(token)
            .getBody();
}
```

如果过期或签名不对，抛出异常，返回 401 Unauthorized。

---

## 3. JWT 在前端的操作

### （1）登录获取 JWT

前端登录时：

```js
fetch("/api/login", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ username: "admin", password: "123456" })
})
.then(res => res.json())
.then(data => {
  localStorage.setItem("token", data.token); // 保存 JWT
});
```

### （2）请求时携带 JWT

前端在请求需要认证的接口时，把 JWT 放到 `Authorization` 头里：

```js
const token = localStorage.getItem("token");

fetch("/api/user/info", {
  headers: {
    "Authorization": "Bearer " + token
  }
})
.then(res => res.json())
.then(data => console.log(data));
```

### （3）登出操作

* 删除本地 `token`（从 `localStorage` 或 `sessionStorage` 移除）。
* 后端一般不保存状态（JWT 无状态），登出只需前端清除。
* 如果需要立即失效，可在后端维护 **黑名单（token blacklist）**。

---

## 4. JWT 与 Session 的对比

| 特点     | Session-Cookie         | JWT                                  |
| -------- | ---------------------- | ------------------------------------ |
| 存储位置 | 服务器内存/数据库      | 前端（localStorage、sessionStorage） |
| 无状态   | ❌（需要服务端保存）    | ✅（无需保存）                        |
| 扩展性   | 集群需共享 Session     | 适合分布式服务                       |
| 安全性   | HttpOnly Cookie 较安全 | 前端存储有被窃取风险                 |
| 失效控制 | 服务端可随时销毁       | 只能等过期或维护黑名单               |

---

## 5. 常见安全注意事项

1. **避免存储敏感信息**：JWT Payload 是可解码的，不能放密码。
2. **设置合理过期时间**：避免长期有效。
3. **使用 HTTPS**：防止中间人窃取。
4. **存储位置**：

   * 推荐 **HttpOnly Cookie**（比 localStorage 更安全，不可被 JS 直接读取）。
   * 如果存 `localStorage`，要防范 XSS。
5. **刷新机制**：

   * 短期 `access_token` + 长期 `refresh_token`（后端验证后发新 token）。

---

要不要我帮你画一个 **JWT 前后端交互流程图（登录 -> 发 token -> 验证 -> 刷新）**？这样更直观。