你说的 **DNS 流程命令**，可以分成两个部分来看：

1. **DNS 解析流程**（从用户输入域名到拿到 IP）
2. **Linux/网络环境里查看 DNS 解析过程的命令**

---

## 1. DNS 解析流程（逐步）

当你在浏览器输入 `www.example.com` 时，流程大致是：

1. **浏览器缓存**：浏览器会先查自己缓存过的域名解析结果。
2. **操作系统缓存**：如果浏览器没有，就问操作系统（`/etc/hosts` 和 DNS 缓存）。
3. **本地域名服务器（Local DNS Resolver）**：

   * 通常是 ISP 或本机配置的 DNS 服务器。
   * 如果本地域名服务器也没有，就会 **递归查询**：

     * 根域名服务器（.）
     * 顶级域名服务器（如 `.com`）
     * 权威域名服务器（example.com 的权威服务器）
   * 最终返回 IP 地址。
4. **操作系统把结果缓存**（减少下次查询开销）。
5. **浏览器收到 IP，开始 TCP/HTTP/HTTPS 连接**。

---

## 2. 常用 DNS 调试命令

### 🔎 查看单次解析

* **nslookup**

  ```bash
  nslookup www.example.com
  ```

  显示 IP 地址和使用的 DNS 服务器。

* **dig** （更详细）

  ```bash
  dig www.example.com
  ```

  输出完整查询过程（包括权威服务器信息）。
  递归模拟：

  ```bash
  dig www.example.com +trace
  ```

* **host**

  ```bash
  host www.example.com
  ```

---

### 🔎 查看本机 DNS 设置 / 缓存

* **Linux 系统 DNS 配置**

  ```bash
  cat /etc/resolv.conf
  ```
* **Windows**

  ```powershell
  ipconfig /displaydns   # 显示 DNS 缓存
  ipconfig /flushdns     # 清空 DNS 缓存
  ```
* **Linux systemd 环境**

  ```bash
  systemd-resolve --status
  resolvectl status
  ```

---

### 🔎 抓包查看 DNS 流量

* 用 tcpdump：

  ```bash
  sudo tcpdump -i any port 53
  ```
* 用 Wireshark：过滤器写 `dns` 就能看到完整的查询和响应。

---

## 3. 快速流程 + 命令对照

```
浏览器缓存 → OS缓存(/etc/hosts) → 本地DNS → 根服务器 → 顶级域服务器 → 权威DNS
       │
       └──► 验证命令: dig/nslookup/host
```

---

要不要我帮你整理一份 **“DNS 故障排查命令清单”**（比如解析慢、解析失败时从本地到权威服务器一步步排查的命令序列）？
