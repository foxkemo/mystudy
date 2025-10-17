在 **Bash** 中，如果你想重复上一条命令的参数，有几个快捷方法：

------

### **1. 使用 `!$`**

`!$` 表示上一条命令的最后一个参数。

```bash
$ cp file.txt /tmp
$ echo !$
# 展开后等同于：
$ echo /tmp
```

------

### **2. 使用 `!^`**

`!^` 表示上一条命令的第一个参数。

```bash
$ cp file.txt /tmp
$ echo !^
# 展开后等同于：
$ echo file.txt
```

------

### **3. 使用 `!\*`**

`!*` 表示上一条命令的所有参数（不包括命令本身）。

```bash
$ cp file1.txt file2.txt /tmp
$ echo !*
# 展开后等同于：
$ echo file1.txt file2.txt /tmp
```

------

### **4. 使用快捷键**

- `Alt + .` （或 `Esc` 后按 `.`）可以快速在命令行中插入上一条命令的最后一个参数，每按一次可以循环选择更早的命令参数。

```bash
$ cp file.txt /tmp
$ echo [按 Alt+.]
# 插入 /tmp
```

------

💡 **小技巧**：结合命令历史和参数引用，你可以快速构造命令，比如：

```bash
$ tar -czf archive.tar.gz folder
$ scp !$ user@remote:/backup
# !$ -> archive.tar.gz
```

------

我可以帮你整理一张 **“Bash 上一条命令参数引用速查表”**，直接记住这些符号就很方便操作。

你希望我画吗？