在 **Debian / Ubuntu / 以及基于它们的 Linux 发行版**中，使用 `apt`（或者 `apt-get`）安装程序时，程序并不是统一放在一个目录，而是根据 **FHS（Filesystem Hierarchy Standard，文件系统层次标准）** 被拆分到系统目录中。常见情况如下：

---

### 🔹 程序可执行文件

* **系统级命令**：
  `/usr/bin/`
  `/usr/sbin/`
  `/bin/`（旧标准，现代系统多为符号链接到 `/usr/bin/`）

* **例子**：
  `apt install curl` → `/usr/bin/curl`
  `apt install nginx` → `/usr/sbin/nginx`

---

### 🔹 配置文件

* **存放在** `/etc/程序名/`
* **例子**：
  `nginx` → `/etc/nginx/`
  `mysql` → `/etc/mysql/`

---

### 🔹 库文件

* **存放在** `/usr/lib/程序名/` 或 `/lib/`
* **例子**：
  `python3` → `/usr/lib/python3.x/`

---

### 🔹 数据文件 / 共享文件

* **存放在** `/usr/share/程序名/`
* **例子**：
  `man` 手册、图标、国际化翻译文件等。

---

### 🔹 日志文件

* **存放在** `/var/log/程序名/`
* **例子**：
  `nginx` → `/var/log/nginx/`
  `mysql` → `/var/log/mysql/`

---

### 🔹 包的安装位置查询方法

1. 查看包安装了哪些文件：

   ```bash
   dpkg -L 包名
   ```

   例：

   ```bash
   dpkg -L curl
   ```

   会列出 curl 的二进制、文档、man 文件等具体路径。

2. 查看某个文件属于哪个包：

   ```bash
   dpkg -S /usr/bin/curl
   ```

---

👉 总结：
`apt` 安装的程序没有统一目录，而是按 **FHS 标准**拆分存放：

* 可执行文件在 `/usr/bin`、`/usr/sbin`
* 配置在 `/etc`
* 库在 `/usr/lib`
* 数据在 `/usr/share`
* 日志在 `/var/log`

要找具体软件的安装目录，推荐用 **`dpkg -L 包名`**。

要不要我帮你写一个 **快速脚本**，输入包名就能直接列出主程序的路径？