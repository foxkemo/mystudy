好的！我们来系统地聊聊 **Shell 中的管道（Pipe）**，这个是 Linux/Unix 系统中非常强大的工具，掌握之后可以极大地提升你写脚本和操作系统的效率。

---

## 🧪 什么是 Shell 管道（Pipe）？

### 🔧 基本语法：
```bash
command1 | command2
```

### 🧠 意思：
把 `command1` 的输出（stdout）**传递给** `command2` 的输入（stdin）。

---

## 🧴 管道的几个核心应用场景

### 🧾 示例 1：简单输出过滤
```bash
ps aux | grep nginx
```
- `ps aux`：查看所有进程
- `grep nginx`：筛选出包含 "nginx" 的行

---

### 📊 示例 2：统计结果数量
```bash
ls /etc | wc -l
```
- `ls /etc`：列出 /etc 目录下的文件
- `wc -l`：统计行数，也就是文件/目录的个数

---

### 📦 示例 3：查看日志最新10行中含有 error 的
```bash
tail -n 10 /var/log/syslog | grep error
```

---

### 🧼 示例 4：配合 awk 和 sort 使用
```bash
du -sh * | sort -h
```
- `du -sh *`：查看当前目录下所有文件/文件夹的大小
- `sort -h`：按照人类可读的大小排序（10K、1M、2G）

---

## 🔁 管道链条：可以串联多个命令
```bash
cat access.log | grep "404" | awk '{print $1}' | sort | uniq -c | sort -nr
```

这个命令做了什么？
1. 读取日志文件
2. 找出 404 的请求
3. 提取 IP 地址（第一个字段）
4. 去重并计数
5. 按数量从大到小排序

👉 常见于 Web 日志分析。

---

## 🔄 管道与 tee 联用（分支输出）

有时候你想让数据 **既能传到下一步，又能存下来**，可以用 `tee`：

```bash
ps aux | tee processes.txt | grep nginx
```

- `tee processes.txt`：把进程列表保存到文件中
- 同时继续将输出传递给 `grep nginx` 筛选结果

---

## 🚨 注意事项

### 1. 管道只传递标准输出（stdout），**不传递标准错误（stderr）**：
```bash
somecommand 2>&1 | tee log.txt
```
`2>&1` 表示把错误输出也合并到标准输出。

---

### 2. 管道右边的命令只接收“上一个命令”的输出，不会回头。

---

## ✨ Bonus：进阶符号组合（常搭配管道）

| 符号         | 说明                          |
|--------------|-------------------------------|
| `|`          | 管道，把前一个命令的输出传给后一个命令 |
| `>`          | 重定向标准输出到文件（覆盖）   |
| `>>`         | 重定向标准输出到文件（追加）   |
| `<`          | 从文件读取作为标准输入         |
| `2>`         | 重定向错误输出（stderr）       |
| `&>`         | 重定向标准+错误输出            |
| `2>&1`       | 把 stderr 重定向到 stdout      |

---

## 想动手试试？

可以试试这些练手命令：

```bash
echo "hello world" | tr 'a-z' 'A-Z'
```

或者：

```bash
ls /bin | grep sh | sort
```

---

想深入到重定向、子进程运行机制、或管道在 bash 脚本中的实际用法，也可以告诉我～我可以带你玩得更深入 😎

非常好，那我们就来更深入地讲讲你提到的这几个核心操作：`tee`、Here Document、以及 `systemctl` 等其他命令。每一个在 shell 脚本中都很常见而且非常有用。

---

## 🔍 一、`tee` 命令详解

### 🧠 功能：
`tee` 会 **读取标准输入（stdin）并写入文件（或多个文件）**，同时也会把内容输出到标准输出（也就是终端）。

### 📝 常见用法：
```bash
echo "hello" | tee file.txt
```
等价于：
```bash
echo "hello" > file.txt
```
但 `tee` 的优势是可以在管道中使用，**结合 sudo 写入文件时非常有用**！

### ⚠️ sudo 场景：
```bash
echo "text" > /etc/somefile       # ❌ 失败：> 是由普通用户执行的
echo "text" | sudo tee /etc/somefile   # ✅ 成功：tee 在 sudo 权限下写文件
```

---

## 📄 二、Here Document (`<<EOF`) 详解

### 🧠 功能：
Here Document（缩写：heredoc）是 Shell 的一种方式，用来把一整段多行文本（甚至是代码）传入某个命令的标准输入。

### 📝 基本语法：
```bash
cat <<EOF
line 1
line 2
EOF
```

也可以这样配合 `tee` 使用（你提供的例子）：
```bash
sudo tee /etc/somefile <<EOF
内容行1
内容行2
EOF
```

> `<<EOF` 表示：**从这里开始直到遇到 `EOF` 为止，所有内容作为输入传入前面的命令。**

你也可以用任意关键字代替 `EOF`，比如：
```bash
cat <<THE_END
Hello
World
THE_END
```

---

## 🔧 三、`systemctl daemon-reload` 和 `systemctl restart`

### 🔄 `systemctl daemon-reload`
- 通知 systemd 重新读取配置文件（如 unit 文件的变更，或者 Docker 的配置文件）。
- 对服务文件（`*.service`）或全局配置更改之后都需要执行一次。

### 🔁 `systemctl restart docker`
- 重新启动 Docker 服务，使配置变更（如 `daemon.json`）生效。

---

## 🔐 四、sudo 总结

```bash
sudo command
```

- 以 root 权限执行命令（Super User DO）
- 通常用于需要访问系统文件、启动服务、安装软件等高权限操作
- 会要求输入当前用户的密码（前提是当前用户有 sudo 权限）

---

## ✅ 举个完整流程的例子

比如要创建一个包含多行内容的配置文件 `/etc/myapp/config.conf`：

```bash
sudo mkdir -p /etc/myapp

sudo tee /etc/myapp/config.conf <<EOF
[settings]
username = admin
password = 123456
EOF

sudo systemctl daemon-reload
sudo systemctl restart myapp
```

---

如果你想，我还可以讲讲比如 `cat > file <<EOF` 和 `echo > file` 的区别、或 shell 中变量嵌入 Here Document 的技巧。你需要吗？