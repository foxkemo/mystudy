明白了 👍，你想系统了解 **MySQL 存储过程里的游标（Cursor）与处理器（Handler）**。
这两者是高级流程控制的重要工具，通常用于 **逐行处理查询结果** 和 **异常/结束控制**。

---

# 🧩 一、游标（Cursor）

## 1️⃣ 什么是游标？

* **游标**是一个指针，用于 **遍历 SELECT 查询返回的多行结果集**。
* MySQL 中 **存储过程无法直接遍历多行结果**，所以需要游标。

### 🔹 使用场景

* 遍历表数据并逐行处理
* 执行复杂统计或业务逻辑
* 在存储过程里模拟“逐行循环”

---

## 2️⃣ 游标的基本语法

```sql
-- 1. 声明游标
DECLARE cursor_name CURSOR FOR SELECT ...;

-- 2. 打开游标
OPEN cursor_name;

-- 3. 取数据到变量
FETCH cursor_name INTO var1, var2, ...;

-- 4. 关闭游标
CLOSE cursor_name;
```

### 🔹 注意事项

* 游标必须在 `BEGIN ... END` 内声明
* **FETCH 每次只取一行**
* 需要 **循环配合 LEAVE / HANDLER** 才能处理多行

---

## 3️⃣ 示例：遍历用户表

```sql
DELIMITER //

CREATE PROCEDURE list_users()
BEGIN
    DECLARE done INT DEFAULT 0;          -- 是否结束标志
    DECLARE uid INT;
    DECLARE uname VARCHAR(50);

    -- 声明游标
    DECLARE cur CURSOR FOR SELECT id, username FROM user;

    -- 声明处理器，游标到末尾时自动设置 done = 1
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = 1;

    OPEN cur;

    read_loop: LOOP
        FETCH cur INTO uid, uname;
        IF done = 1 THEN
            LEAVE read_loop;    -- 游标结束，退出循环
        END IF;
        -- 处理逻辑，例如打印或插入日志
        SELECT CONCAT('用户ID:', uid, ', 用户名:', uname);
    END LOOP;

    CLOSE cur;
END //

DELIMITER ;
```

调用：

```sql
CALL list_users();
```

---

# 🧩 二、Handler（处理器）

## 1️⃣ 什么是 Handler？

* **Handler 是异常或特殊条件的处理机制**，类似于编程语言里的 `try...catch`。
* 在游标或事务中，经常用它来 **捕获异常或控制循环结束**。

### 🔹 类型

| 类型                 | 描述             |
| ------------------ | -------------- |
| `CONTINUE HANDLER` | 捕获后继续执行下一条语句   |
| `EXIT HANDLER`     | 捕获后退出存储过程/函数或块 |
| `UNDO HANDLER`     | MySQL 不支持      |

### 🔹 条件

* `SQLEXCEPTION`：任何 SQL 错误
* `SQLWARNING`：SQL 警告
* `NOT FOUND`：游标取不到数据（常用于循环结束）
* `SQLSTATE 'xxxx'`：指定错误码

---

## 2️⃣ 示例：异常处理 + 游标结束

```sql
DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = 1;
```

作用：

* 当游标 `FETCH` 没有数据返回时，不报错，而是把 `done` 置为 `1`
* 在循环中用 `IF done=1 THEN LEAVE loop; END IF;` 退出循环

---

## 3️⃣ 综合示例：遍历用户余额

```sql
DELIMITER //

CREATE PROCEDURE check_balance()
BEGIN
    DECLARE done INT DEFAULT 0;
    DECLARE uid INT;
    DECLARE bal DECIMAL(10,2);

    DECLARE cur CURSOR FOR SELECT id, balance FROM account;
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = 1;

    OPEN cur;

    read_loop: LOOP
        FETCH cur INTO uid, bal;
        IF done = 1 THEN
            LEAVE read_loop;
        END IF;

        -- 业务逻辑：余额小于 100 记录警告
        IF bal < 100 THEN
            INSERT INTO balance_warning(user_id, warning_msg)
            VALUES(uid, CONCAT('余额不足：', bal));
        END IF;
    END LOOP;

    CLOSE cur;
END //

DELIMITER ;
```

调用：

```sql
CALL check_balance();
```

---

# 🧩 四、游标与 Handler 配合的流程总结

1. **声明变量**：用于存储每行数据 + 游标结束标志
2. **声明游标**：绑定 SELECT 查询
3. **声明 Handler**：捕获 `NOT FOUND`，避免循环报错
4. **打开游标**
5. **循环 FETCH**：逐行取数据
6. **判断结束**：`done = 1` 则退出循环
7. **关闭游标**

---

# 💡 五、总结对比

| 特性    | 游标            | Handler    |
| ----- | ------------- | ---------- |
| 功能    | 遍历多行结果集       | 异常或特殊条件处理  |
| 与变量关系 | FETCH INTO 变量 | 通过变量控制流程   |
| 常用配合  | 循环 + 游标       | 游标末尾或异常处理  |
| 调用方式  | 存储过程内使用       | 存储过程或函数内声明 |

---

如果你愿意，我可以帮你画一张 **“游标 + Handler 执行流程图”**，
直观显示 **FETCH → Handler → LOOP → 处理逻辑 → CLOSE** 的流程，一目了然。

你希望我画吗？
