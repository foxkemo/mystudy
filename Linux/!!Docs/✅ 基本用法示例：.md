在终端中使用 FTP（File Transfer Protocol）进行文件传输时，可以通过 FTP shell 命令行交互。你可以使用命令如 `ftp` 或 `lftp` 来进入 FTP shell，然后使用一系列命令上传、下载、浏览文件。

------

## ✅ 基本用法示例：

```bash
ftp ftp.example.com
```

连接成功后输入用户名和密码，进入 FTP shell。

------

## 📋 常用 FTP shell 命令：

| 命令           | 作用                             |
| -------------- | -------------------------------- |
| `open [host]`  | 连接到 FTP 服务器                |
| `user`         | 提供用户名（有时也自动提示输入） |
| `ls`           | 查看当前目录下的文件列表         |
| `cd`           | 进入服务器目录                   |
| `lcd`          | 更改本地工作目录                 |
| `get [file]`   | 下载文件（从服务器到本地）       |
| `mget [file*]` | 批量下载文件（支持通配符）       |
| `put [file]`   | 上传文件（从本地到服务器）       |
| `mput [file*]` | 批量上传文件                     |
| `delete`       | 删除服务器上的文件               |
| `mkdir`        | 创建服务器目录                   |
| `rmdir`        | 删除服务器目录                   |
| `pwd`          | 显示远程当前目录                 |
| `lpwd`         | 显示本地当前目录                 |
| `binary`       | 设置为二进制传输模式（推荐）     |
| `ascii`        | 设置为 ASCII 文本模式传输        |
| `bye` / `quit` | 断开连接并退出 FTP               |
| `help`         | 查看帮助                         |

------

## 📌 示例操作流程：

```bash
ftp ftp.example.com
# 用户名：user
# 密码：*******

binary            # 切换为二进制模式（传输图片、压缩包等）
cd /remote/path   # 进入服务器目录
lcd /local/path   # 切换本地目录
get file.txt      # 下载文件
put file2.txt     # 上传文件
bye               # 退出 FTP
```

------

## ⚠️ 注意事项：

- 如果要传输的是**图片、音频、压缩包等非文本文件**，请务必执行 `binary` 命令；
- `mget` 和 `mput` 批量操作时可能需要确认每个文件（可以用 `prompt` 命令关闭提示）；
- 默认 FTP 不加密，推荐使用 SFTP（安全的 FTP over SSH）。

------

## 🔐 安全建议：

FTP 是明文传输，**不安全**，若条件允许，建议使用：

- `sftp`（推荐）：

  ```bash
  sftp user@host
  ```

- 或 `lftp`，支持 FTP/SFTP/FTPS：

  ```bash
  lftp sftp://user@host
  ```

------

如果你告诉我你是用哪个 FTP 客户端（如：命令行 `ftp`、`lftp`、`sftp`、FileZilla 等），我可以给出更精确的命令和操作说明。是否需要？