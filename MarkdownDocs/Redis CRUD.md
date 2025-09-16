你想要的 **Redis 的 CRUD（增删改查）** 我来帮你整理成一个最常用的命令速查表：

---

# 🔑 Redis CRUD 操作

## 1. Create（增）

向 Redis 插入数据：

```sh
SET key value               # 设置字符串
SETEX key 60 value          # 设置字符串并设置 60 秒过期时间
HSET user:1 name "Tom"      # 设置哈希表字段
LPUSH mylist "a" "b" "c"    # 向列表左侧插入多个值
SADD myset "x" "y" "z"      # 向集合添加元素
ZADD myzset 1 "one" 2 "two" # 向有序集合添加元素（带分数）
```

---

## 2. Read（查）

读取数据：

```sh
GET key                     # 获取字符串
MGET key1 key2 key3         # 批量获取
HGET user:1 name            # 获取哈希表字段
HGETALL user:1              # 获取哈希表所有字段和值
LRANGE mylist 0 -1          # 获取列表所有元素
SMEMBERS myset              # 获取集合所有元素
ZRANGE myzset 0 -1 WITHSCORES # 获取有序集合所有成员和分数
```

---

## 3. Update（改）

更新就是 **覆盖写** 或 **修改部分字段**：

```sh
SET key new_value           # 直接覆盖字符串
APPEND key " world"         # 给字符串追加内容
HSET user:1 age 20          # 修改哈希表字段
LSET mylist 0 "new_a"       # 修改列表指定索引的值
ZINCRBY myzset 2 "one"      # 增加有序集合成员的分数
```

---

## 4. Delete（删）

删除数据：

```sh
DEL key1 key2               # 删除一个或多个 key
HDEL user:1 name age        # 删除哈希表的字段
LPOP mylist                 # 移除并返回列表最左边的元素
RPOP mylist                 # 移除并返回列表最右边的元素
SREM myset "x"              # 从集合中移除元素
ZREM myzset "one"           # 从有序集合中移除元素
```

---

## 5. 其他常用

```sh
EXISTS key                  # 判断 key 是否存在
TTL key                     # 查看 key 的剩余过期时间
KEYS *                      # 获取所有 key（生产环境不推荐）
SCAN 0                      # 分批获取 key（推荐替代 KEYS）
```

---

⚡ 总结：

* **字符串 (String)**：`SET / GET / DEL / APPEND`
* **哈希 (Hash)**：`HSET / HGET / HGETALL / HDEL`
* **列表 (List)**：`LPUSH / LRANGE / LSET / LPOP`
* **集合 (Set)**：`SADD / SMEMBERS / SREM`
* **有序集合 (ZSet)**：`ZADD / ZRANGE / ZREM / ZINCRBY`

---

要不要我给你写一个 **Java SpringBoot + RedisTemplate** 的 CRUD 示例代码，方便你在后端项目里直接用？