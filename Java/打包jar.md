Java 项目打包成 JAR 文件，常用几种方式，以下是命令行打包的常见命令示例：

------

## 1. 编译 Java 文件

```bash
javac -d out src/com/example/*.java
```

- `-d out`：编译后的 `.class` 文件放入 `out` 目录
- `src/com/example/*.java`：Java 源文件路径

------

## 2. 创建 JAR 包

```bash
jar cvf myapp.jar -C out .
```

参数说明：

- `c`：create，创建 Jar 包
- `v`：verbose，显示详细信息
- `f`：file，指定文件名
- `-C out .`：进入 `out` 目录，将该目录下所有内容打包

------

## 3. 创建可执行 JAR 包（指定入口类）

1. 先写一个 `MANIFEST.MF` 文件，内容示例：

```
Manifest-Version: 1.0
Main-Class: com.example.MainClass
```

1. 打包时指定 `MANIFEST.MF`：

```bash
jar cvfm myapp.jar MANIFEST.MF -C out .
```

------

## 4. 一步命令（假设只有一个 main 类）

```bash
javac -d out src/com/example/MainClass.java
jar cfe myapp.jar com.example.MainClass -C out .
```

参数说明：

- `e`：entry point，指定主类（Main-Class）

------

## 5. 常用辅助命令

- 查看 Jar 内容：

  ```bash
  jar tf myapp.jar
  ```

- 解压 Jar：

  ```bash
  jar xf myapp.jar
  ```

