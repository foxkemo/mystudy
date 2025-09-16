好的 👍 我给你整理一份 **Redis 常用命令速查表**，分门别类，方便查阅。

---

## 🔑 1. 基础操作

```bash
SET key value          # 设置 key 的值
GET key                # 获取 key 的值
DEL key                # 删除 key
EXISTS key             # 判断 key 是否存在（1 存在，0 不存在）
KEYS pattern           # 查找所有符合 pattern 的 key，例如：KEYS user:*
EXPIRE key seconds     # 设置过期时间（秒）
TTL key                # 查看剩余过期时间（-1 表示永不过期，-2 表示已过期）
PERSIST key            # 移除过期时间（变为永久）
```

---

## 📂 2. 字符串 (String)

```bash
APPEND key value       # 追加字符串
MSET k1 v1 k2 v2       # 批量设置
MGET k1 k2             # 批量获取
INCR key               # 将值加 1（整数）
DECR key               # 将值减 1
INCRBY key 5           # 增加指定整数
DECRBY key 5           # 减少指定整数
GETRANGE key 0 4       # 截取字符串（返回前 5 个字符）
SETRANGE key 6 "abc"   # 从指定位置修改内容
```

---

## 📋 3. 哈希 (Hash)

```bash
HSET user:1 name "Tom" age 20  # 设置字段
HGET user:1 name               # 获取字段
HDEL user:1 age                # 删除字段
HGETALL user:1                 # 获取所有字段和值
HKEYS user:1                   # 获取所有字段名
HVALS user:1                   # 获取所有字段值
HEXISTS user:1 name            # 判断字段是否存在
HINCRBY user:1 age 1           # 字段值增加
```

---

## 📚 4. 列表 (List)

```bash
LPUSH list a b c       # 从左边插入（结果 c b a）
RPUSH list x y z       # 从右边插入（结果 c b a x y z）
LPOP list              # 从左边弹出
RPOP list              # 从右边弹出
LRANGE list 0 -1       # 获取所有元素（0 表示起始，-1 表示最后一个）
LINDEX list 2          # 获取指定下标的值
LLEN list              # 获取列表长度
LREM list 2 a          # 删除两个值为 a 的元素
LTRIM list 0 2         # 只保留前 3 个元素
```

---

## 📌 5. 集合 (Set)

```bash
SADD set a b c         # 添加元素
SMEMBERS set           # 查看所有元素
SISMEMBER set a        # 判断元素是否存在
SREM set b             # 删除元素
SCARD set              # 获取集合大小
SPOP set               # 随机移除一个元素
SRANDMEMBER set 2      # 随机获取 2 个元素
SINTER set1 set2       # 求交集
SUNION set1 set2       # 求并集
SDIFF set1 set2        # 求差集
```

---

## 🔢 6. 有序集合 (Sorted Set)

```bash
ZADD zset 1 a 2 b 3 c  # 添加元素（分数+值）
ZRANGE zset 0 -1 WITHSCORES  # 按分数升序查询
ZREVRANGE zset 0 -1 WITHSCORES # 按分数降序查询
ZSCORE zset a          # 获取元素分数
ZREM zset b            # 删除元素
ZCARD zset             # 获取数量
ZINCRBY zset 2 a       # 分数增加
ZRANK zset a           # 获取排名（升序）
ZREVRANK zset a        # 获取排名（降序）
```

---

## 🛠 7. 事务 & 脚本

```bash
MULTI                  # 开启事务
SET a 1
SET b 2
EXEC                   # 执行事务
DISCARD                # 放弃事务

EVAL "return redis.call('GET', KEYS[1])" 1 key1
```

---

## 🔍 8. 管理命令

```bash
FLUSHDB                # 清空当前数据库
FLUSHALL               # 清空所有数据库
DBSIZE                 # 查看 key 数量
INFO                   # 查看 Redis 运行信息
MONITOR                # 实时监控命令
CONFIG GET *           # 查看所有配置
CONFIG SET maxmemory 128mb # 修改配置
```

---

⚡ 常见用法：

* 缓存：`SET key value EX 60` （带过期时间的缓存）
* 分布式锁：`SETNX lock_key 1`
* 排行榜：用 `ZADD` + `ZRANGE` 实现

---

要不要我帮你整理一个 **Redis 常用命令速查表 PDF**，方便你本地保存和随时查阅？