明白啦～你是要 **SFTP（Secure File Transfer Protocol）常用命令** 的速查表对吧 👍。

SFTP 是基于 SSH 的文件传输工具，命令和 Linux Shell 类似。下面给你整理常用命令：

---

## 🔑 连接与退出

```bash
sftp user@host             # 连接远程服务器
sftp -P 2222 user@host     # 指定端口（默认 22）
sftp user@192.168.1.10:/dir # 直接进入远程目录

help                       # 查看所有命令
exit / quit                # 退出 SFTP
```

---

## 📂 本地与远程目录操作

```bash
pwd                        # 显示远程当前目录
lpwd                       # 显示本地当前目录

cd /remote/path            # 切换远程目录
lcd /local/path            # 切换本地目录

ls /ll                     # 列出远程目录文件
lls                        # 列出本地目录文件
```

---

## 📥 下载文件

```bash
get remote.txt             # 下载单个文件
get remote.txt local.txt   # 下载并改名
get -r /remote/dir         # 递归下载整个目录
```

---

## 📤 上传文件

```bash
put local.txt              # 上传单个文件
put local.txt remote.txt   # 上传并改名
put -r /local/dir          # 递归上传目录
```

---

## 🛠 文件与目录管理

```bash
mkdir newdir               # 创建远程目录
rmdir olddir               # 删除远程目录
rm file.txt                # 删除远程文件
rename old.txt new.txt     # 远程文件重命名
chmod 644 file.txt         # 修改远程文件权限
```

---

## 🔍 其他常用

```bash
df -h                      # 查看远程磁盘空间
stat file.txt              # 查看远程文件信息
!ls                        # 在本地执行 shell 命令（例如列出本地文件）
```

---

💡小技巧：

* 如果你习惯 **scp**，也可以直接用：

  ```bash
  scp file.txt user@host:/path/
  scp -r dir user@host:/path/
  ```

---

要不要我帮你做一个 **SFTP vs SCP 常用命令对照表**？这样你用的时候更快查找。