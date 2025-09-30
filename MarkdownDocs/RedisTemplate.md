👌你这个示例基本把 **`RedisTemplate` 的常见用法**都展示了。
我帮你总结一下 **`RedisTemplate` 常见的衍生对象（opsXXX）以及对应的操作方法**，方便你记忆和查阅。

---

# ✅ RedisTemplate 衍生对象与常见操作

在 Spring Data Redis 里，`RedisTemplate` 提供了五种常见数据类型的操作接口，以及一些通用命令。

---

## 1. **字符串（String）**

👉 **接口**：`ValueOperations<K, V> valueOps = redisTemplate.opsForValue();`

常用方法（对应 Redis 字符串命令）：

* `set(key, value)` → `SET`
* `get(key)` → `GET`
* `set(key, value, timeout, unit)` → `SETEX`
* `setIfAbsent(key, value)` → `SETNX`

示例：

```java
redisTemplate.opsForValue().set("name", "小明");
String val = redisTemplate.opsForValue().get("name");
```

---

## 2. **哈希（Hash）**

👉 **接口**：`HashOperations<K, HK, HV> hashOps = redisTemplate.opsForHash();`

常用方法（对应 Redis 哈希命令）：

* `put(key, hashKey, value)` → `HSET`
* `get(key, hashKey)` → `HGET`
* `delete(key, hashKeys...)` → `HDEL`
* `keys(key)` → `HKEYS`
* `values(key)` → `HVALS`

示例：

```java
hashOps.put("100", "name", "Tom");
String name = (String) hashOps.get("100", "name");
```

---

## 3. **列表（List）**

👉 **接口**：`ListOperations<K, V> listOps = redisTemplate.opsForList();`

常用方法（对应 Redis 列表命令）：

* `leftPush(key, value)` → `LPUSH`
* `rightPush(key, value)` → `RPUSH`
* `leftPushAll(key, values...)` → `LPUSH`
* `range(key, start, end)` → `LRANGE`
* `rightPop(key)` → `RPOP`
* `size(key)` → `LLEN`

示例：

```java
listOps.leftPushAll("mylist", "a", "b", "c");
List<String> values = listOps.range("mylist", 0, -1);
```

---

## 4. **集合（Set）**

👉 **接口**：`SetOperations<K, V> setOps = redisTemplate.opsForSet();`

常用方法（对应 Redis 集合命令）：

* `add(key, values...)` → `SADD`
* `members(key)` → `SMEMBERS`
* `size(key)` → `SCARD`
* `intersect(key, otherKey)` → `SINTER`
* `union(key, otherKey)` → `SUNION`
* `remove(key, values...)` → `SREM`

示例：

```java
setOps.add("set1", "a", "b", "c");
Set<String> members = setOps.members("set1");
```

---

## 5. **有序集合（ZSet / Sorted Set）**

👉 **接口**：`ZSetOperations<K, V> zsetOps = redisTemplate.opsForZSet();`

常用方法（对应 Redis 有序集合命令）：

* `add(key, value, score)` → `ZADD`
* `range(key, start, end)` → `ZRANGE`
* `incrementScore(key, value, delta)` → `ZINCRBY`
* `remove(key, values...)` → `ZREM`

示例：

```java
zsetOps.add("zset1", "a", 10);
Set<String> values = zsetOps.range("zset1", 0, -1);
```

---

## 6. **通用命令**

👉 直接用 `RedisTemplate` 提供：

常用方法（对应 Redis 通用命令）：

* `keys(pattern)` → `KEYS`
* `hasKey(key)` → `EXISTS`
* `type(key)` → `TYPE`
* `delete(key)` → `DEL`

示例：

```java
Set keys = redisTemplate.keys("*");
Boolean exist = redisTemplate.hasKey("name");
redisTemplate.delete("mylist");
```

---

# 📌 总结表格

| Redis 类型 | RedisTemplate 衍生对象 | 常见方法                                         | 对应命令                                        |
| -------- | ------------------ | -------------------------------------------- | ------------------------------------------- |
| String   | `opsForValue()`    | set, get, setIfAbsent, setEx                 | SET, GET, SETNX, SETEX                      |
| Hash     | `opsForHash()`     | put, get, delete, keys, values               | HSET, HGET, HDEL, HKEYS, HVALS              |
| List     | `opsForList()`     | leftPush, rightPush, range, rightPop, size   | LPUSH, RPUSH, LRANGE, RPOP, LLEN            |
| Set      | `opsForSet()`      | add, members, size, intersect, union, remove | SADD, SMEMBERS, SCARD, SINTER, SUNION, SREM |
| ZSet     | `opsForZSet()`     | add, range, incrementScore, remove           | ZADD, ZRANGE, ZINCRBY, ZREM                 |
| 通用       | `redisTemplate`    | keys, hasKey, type, delete                   | KEYS, EXISTS, TYPE, DEL                     |

---

要不要我帮你整理一个 **思维导图/结构图**（RedisTemplate → opsForValue/opsForHash/... → 常见方法）？这样查阅会更直观。
