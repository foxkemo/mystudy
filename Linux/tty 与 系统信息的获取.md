

---

## 一、Linux 系统信息获取方式

### 1. 查看基本系统信息

* **内核版本、系统架构**

```bash
uname -a        # 显示所有信息
uname -r        # 内核版本
uname -m        # 硬件架构 (x86_64, arm 等)
uname -n        # 主机名
```

* **系统发行版信息**

```bash
cat /etc/os-release     # 推荐，通用
lsb_release -a          # 部分发行版提供 (需 lsb-core 包)
```

* **CPU、内存信息**

```bash
cat /proc/cpuinfo       # CPU 型号、核数
cat /proc/meminfo       # 内存总量、空闲内存
free -h                 # 直观显示内存
```

* **磁盘信息**

```bash
lsblk                  # 块设备及挂载点
df -h                  # 各挂载点磁盘占用
```

---

## 二、tty 设备信息获取方式

Linux 下 **tty (teletypewriter)** 表示终端设备，分为虚拟终端、伪终端、串口终端。

### 1. 查看当前进程绑定的终端

```bash
tty     # 显示当前 shell 所在的终端，例如 /dev/pts/0
```

### 2. 查看所有终端设备

```bash
ls -l /dev/tty*      # 查看系统下的所有 tty 设备文件
```

* `/dev/tty` ：当前进程的控制终端
* `/dev/tty0`：当前虚拟控制台
* `/dev/tty1` ~ `/dev/ttyN`：系统提供的虚拟终端 (Ctrl+Alt+F1~F6)
* `/dev/pts/N`：伪终端，用于远程登录 (ssh, xterm 等)

### 3. 查看终端连接用户

```bash
who        # 当前登录用户及使用的终端
w          # 用户+执行的命令
```

### 4. 查看终端详细信息

```bash
stty -a     # 显示当前终端的参数设置 (行模式、回显等)
```

### 5. 串口终端（物理 ttyS*）

```bash
dmesg | grep tty       # 系统日志里查看串口设备识别
setserial -g /dev/ttyS*  # 查看串口硬件配置
```

---

✅ 总结：

* 系统信息主要用 `uname`、`cat /proc/*`、`lsblk`、`df` 等。
* tty 信息主要用 `tty`、`who`、`stty`、`ls -l /dev/tty*`。

---

