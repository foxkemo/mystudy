你提到的 `CONCAT` 在 **MySQL** 和 **MyBatis** 模糊查询里非常常用，用来把 `%` 和参数拼接在一起。下面我详细讲解一下。

------

## 1️⃣ MySQL 中的 CONCAT

`CONCAT` 是 MySQL 的字符串拼接函数：

```sql
CONCAT(str1, str2, ..., strN)
```

- 返回所有参数拼接后的字符串
- 如果任意参数为 `NULL`，返回结果为 `NULL`（注意区别 `CONCAT_WS`）

### 示例

```sql
SELECT CONCAT('Hello', ' ', 'World');  -- 结果: 'Hello World'
SELECT CONCAT('%', 'Java', '%');       -- 结果: '%Java%'
```

------

## 2️⃣ 在 MyBatis 模糊查询中使用 CONCAT

### XML 示例

```xml
<select id="findByTitle" resultType="Article">
    SELECT * FROM article
    WHERE title LIKE CONCAT('%', #{str}, '%')
</select>
```

- 这里 `#{str}` 是 MyBatis 占位符，JDBC 会安全绑定参数
- SQL 最终执行时类似：

```sql
SELECT * FROM article WHERE title LIKE CONCAT('%', 'Java', '%');
-- 相当于 WHERE title LIKE '%Java%'
```

✅ 好处：

- 不用在 Java 里拼 `%`
- 安全防止 SQL 注入
- MyBatis 不会把 `%#{str}%` 当作普通占位符处理错误

------

### 可扩展：多字段模糊查询

```xml
<select id="searchArticle" resultType="Article">
    SELECT * FROM article
    WHERE title LIKE CONCAT('%', #{keyword}, '%')
       OR content LIKE CONCAT('%', #{keyword}, '%')
</select>
```

- 可以同时匹配 `title` 或 `content` 字段

------

如果你愿意，我可以帮你写一个 **MyBatis + MySQL 完整模糊查询模板**，前端传入关键字即可支持多字段查询和分页。

你希望我直接写这个模板吗？