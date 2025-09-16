Redis 的配置文件一般是 `redis.conf`，里面包含了 Redis 服务运行时的各种参数控制。下面我给你一个结构化的整理，方便你查阅和修改：

---

## 1. 基本配置

```conf
# 监听的 IP 地址，默认只监听本机 (127.0.0.1)，改成 0.0.0.0 可以远程访问
bind 127.0.0.1

# Redis 监听的端口，默认 6379
port 6379

# 守护进程模式 (后台运行)，默认 no
daemonize yes

# Redis 进程 ID 文件存放位置
pidfile /var/run/redis_6379.pid

# 日志级别: debug, verbose, notice, warning
loglevel notice

# 日志文件位置（默认空表示打印到标准输出）
logfile /var/log/redis/redis-server.log
```

---

## 2. 内存管理

```conf
# 最大内存大小，超出后根据 maxmemory-policy 策略处理
maxmemory 512mb

# 内存淘汰策略:
# noeviction: 不删除，直接返回错误
# allkeys-lru: 从所有 key 中淘汰最少使用的
# volatile-lru: 从设置了过期时间的 key 中淘汰最少使用的
maxmemory-policy allkeys-lru
```

---

## 3. 持久化

```conf
# RDB 快照持久化，满足条件时触发保存
save 900 1    # 900 秒内至少 1 次写入
save 300 10   # 300 秒内至少 10 次写入
save 60 10000 # 60 秒内至少 10000 次写入

# RDB 文件名
dbfilename dump.rdb

# RDB 文件保存目录
dir /var/lib/redis

# 是否在保存出错时停止写入
stop-writes-on-bgsave-error yes

# 是否压缩 RDB 文件
rdbcompression yes

# 是否使用校验和验证 RDB 文件
rdbchecksum yes

# AOF 持久化，默认关闭
appendonly no

# AOF 文件名
appendfilename "appendonly.aof"

# AOF 刷盘策略: always, everysec, no
appendfsync everysec
```

---

## 4. 安全相关

```conf
# 设置密码（推荐开启）
requirepass yourpassword

# 只读副本是否允许写入，默认 no
replica-read-only yes
```

---

## 5. 主从复制

```conf
# 设置主节点信息 (从节点配置)
replicaof <masterip> <masterport>

# 主从复制密码 (主节点的 requirepass)
masterauth yourpassword
```

---

## 6. 高级配置

```conf
# 客户端最大连接数，默认无限制
maxclients 10000

# 客户端空闲超时，超过后断开连接（秒），0 表示永不超时
timeout 300

# 是否开启集群模式
cluster-enabled yes

# 集群配置文件（自动生成）
cluster-config-file nodes.conf

# 集群节点超时时间（毫秒）
cluster-node-timeout 5000
```

---

⚡ **使用建议**

* 开发环境可以只开 RDB（快照），生产环境推荐同时开 AOF（更安全）。
* 远程访问一定要配置 `bind 0.0.0.0` + `requirepass`，并加上防火墙限制。
* 内存有限时要设置 `maxmemory` 和淘汰策略，否则可能直接 OOM。

---

要不要我给你整理一个 **适合开发环境** 和一个 **适合生产环境** 的 `redis.conf` 模板？