好问题 👍，在 MyBatis 里，**当 SQL 查询返回多条结果时，返回值类型和结果映射规则如下**：

------

## 1. **返回 Java 基本类型 / 单值类型**

```java
int count();
<select id="count" resultType="int">
  SELECT COUNT(*) FROM dish
</select>
```

👉 SQL 结果必须只有一行一列，否则会抛异常：

```
org.apache.ibatis.exceptions.TooManyResultsException: Expected one result (or null) to be returned by selectOne(), but found: 3
```

------

## 2. **返回 JavaBean / VO 对象**

```java
DishVO getById(Long id);
<select id="getById" resultType="com.sky.vo.DishVO">
  SELECT id, name, price FROM dish WHERE id = #{id}
</select>
```

👉 如果 SQL 返回多条记录，MyBatis 依旧会报 `TooManyResultsException`，因为它期望的是单个对象。

------

## 3. **返回 List（多条结果 → List 封装）**

```java
List<DishVO> pageQuery();
<select id="pageQuery" resultType="com.sky.vo.DishVO">
  SELECT id, name, price FROM dish WHERE status = 1
</select>
```

👉 这里会把每一行映射成一个 `DishVO`，最后返回一个 `List<DishVO>`。
 即使结果为 0 条，返回的也是 **空 List**，不会是 `null`。

------

## 4. **返回 Map**

- **单条记录 → Map**（列名 → 值）

  ```java
  Map<String, Object> selectAsMap(Long id);
  ```

  ```xml
  <select id="selectAsMap" resultType="map">
    SELECT id, name, price FROM dish WHERE id = #{id}
  </select>
  ```

  👉 只能返回一条，多条会抛异常。

- **多条记录 → Map 集合**

  ```java
  @MapKey("id")
  Map<Long, DishVO> selectAllAsMap();
  ```

  ```xml
  <select id="selectAllAsMap" resultType="com.sky.vo.DishVO">
    SELECT id, name, price FROM dish
  </select>
  ```

  👉 `@MapKey("id")` 指定用某个字段当作 Map 的 key。结果是：

  ```json
  {
    1: {id:1, name:"鱼香肉丝"},
    2: {id:2, name:"宫保鸡丁"}
  }
  ```

------

## 5. 小结

| 方法返回类型                    | SQL 多条结果时行为                        |
| ------------------------------- | ----------------------------------------- |
| **单值类型**（int, String, VO） | 抛 `TooManyResultsException`              |
| **List<T>**                     | 正常返回，结果映射为 List，0 条 → 空 List |
| **Map**（不带 @MapKey）         | 抛异常（多条结果）                        |
| **Map<K,V>** 带 @MapKey         | 正常返回，K 对应某字段，V 对应对象        |

------

⚡ 结论：
 **如果可能返回多条数据 → 方法签名必须用 `List<T>` 或 `Map<K,V>`（带 @MapKey）**。

------

要不要我帮你写一个 **分页查询的完整 Mapper 示例**（比如 `pageQuery` 返回 `List<DishVO>`，配合 PageHelper 使用）？这样就正好对应你前面问的分页场景。