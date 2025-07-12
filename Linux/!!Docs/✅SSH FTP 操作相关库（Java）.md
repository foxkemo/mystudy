在 Java 中，与 **FTP** 和 **SSH** 操作相关的库很多，最常用的有以下几个：

------

## ✅ 一、FTP 操作相关库（Java）

### 1. **Apache Commons Net**

- 最常用的 FTP 操作库，支持 FTP、FTPS、Telnet、SMTP、POP3、IMAP 等协议。
- Maven 依赖：

```xml
<dependency>
    <groupId>commons-net</groupId>
    <artifactId>commons-net</artifactId>
    <version>3.9.0</version>
</dependency>
```

### ✅ FTP 基本操作示例（使用 Apache Commons Net）：

```java
import org.apache.commons.net.ftp.FTPClient;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;

public class FTPExample {
    public static void main(String[] args) {
        FTPClient ftpClient = new FTPClient();
        try {
            ftpClient.connect("ftp.example.com", 21);
            ftpClient.login("your_username", "your_password");

            ftpClient.changeWorkingDirectory("/remote/path/");
            FileOutputStream fos = new FileOutputStream("local_file.txt");
            ftpClient.retrieveFile("remote_file.txt", fos);

            fos.close();
            ftpClient.logout();
            ftpClient.disconnect();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

------

## ✅ 二、SFTP（基于 SSH） 操作相关库

### 1. **JSch（Java Secure Channel）**

- 由 JCraft 提供，轻量但较底层。
- 支持 SSH、SCP、SFTP。
- Maven：

```xml
<dependency>
    <groupId>com.jcraft</groupId>
    <artifactId>jsch</artifactId>
    <version>0.1.55</version>
</dependency>
```

### ✅ SFTP 示例（下载文件）：

```java
import com.jcraft.jsch.*;

import java.io.FileOutputStream;
import java.io.InputStream;

public class SFTPExample {
    public static void main(String[] args) {
        String user = "your_user";
        String host = "sftp.example.com";
        int port = 22;
        String privateKeyPath = "/path/to/private/key"; // 或用密码
        String password = "your_password";

        try {
            JSch jsch = new JSch();
            // jsch.addIdentity(privateKeyPath); // 如果用密钥
            Session session = jsch.getSession(user, host, port);
            session.setPassword(password);
            session.setConfig("StrictHostKeyChecking", "no");
            session.connect();

            Channel channel = session.openChannel("sftp");
            channel.connect();
            ChannelSftp sftpChannel = (ChannelSftp) channel;

            InputStream inputStream = sftpChannel.get("remote_file.txt");
            FileOutputStream fos = new FileOutputStream("local_copy.txt");

            byte[] buffer = new byte[1024];
            int read;
            while ((read = inputStream.read(buffer)) > 0) {
                fos.write(buffer, 0, read);
            }

            inputStream.close();
            fos.close();
            sftpChannel.exit();
            session.disconnect();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

------

### 2. **SSHJ**

- 更现代化的 SSH 库，相较于 JSch 更加稳定易用。
- Maven 依赖：

```xml
<dependency>
    <groupId>com.hierynomus</groupId>
    <artifactId>sshj</artifactId>
    <version>0.38.0</version>
</dependency>
```

### ✅ SSHJ SFTP 示例：

```java
import net.schmizz.sshj.SSHClient;
import net.schmizz.sshj.sftp.SFTPClient;

import java.io.File;

public class SSHJSftpExample {
    public static void main(String[] args) throws Exception {
        SSHClient sshClient = new SSHClient();
        sshClient.addHostKeyVerifier((hostname, port, key) -> true); // 禁用验证，仅测试用
        sshClient.connect("sftp.example.com");

        sshClient.authPassword("your_user", "your_password");

        SFTPClient sftpClient = sshClient.newSFTPClient();
        sftpClient.get("remote_file.txt", new File("local_file.txt"));

        sftpClient.close();
        sshClient.disconnect();
    }
}
```

------

## 🧩 三、对比与建议

| 功能需求        | 推荐库               |
| --------------- | -------------------- |
| FTP             | Apache Commons Net   |
| SFTP（SSH）     | SSHJ（优先） or JSch |
| SCP、命令行执行 | SSHJ 支持良好        |
| 简单快速实现    | JSch 简单但较底层    |

------

## 📌 拓展：执行远程 SSH 命令（如 Linux 命令）

### 使用 JSch 示例：

```java
ChannelExec channelExec = (ChannelExec) session.openChannel("exec");
channelExec.setCommand("ls -l /home");
channelExec.setErrStream(System.err);
InputStream in = channelExec.getInputStream();
channelExec.connect();

BufferedReader reader = new BufferedReader(new InputStreamReader(in));
String line;
while ((line = reader.readLine()) != null) {
    System.out.println(line);
}

channelExec.disconnect();
```

------

如需我帮你搭建一个完整的 FTP / SFTP 上传下载工具类或整合 Spring Boot 使用，欢迎继续提问。