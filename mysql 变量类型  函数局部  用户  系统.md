非常好 👍
在 MySQL **存储过程（Stored Procedure）** 或一般 SQL 代码中，**变量的定义与使用** 是非常核心的内容。
下面我来系统讲解：**变量的定义、作用域、赋值方式与使用场景**。
 
---

## 🧩 一、变量的种类（Scope）

| 类型       | 关键字       | 使用范围            | 示例                     |
| -------- | --------- | --------------- | ---------------------- |
| **局部变量** | `DECLARE` | 仅在存储过程 / 函数内部有效 | `DECLARE a INT;`       |
| **用户变量** | `@变量名`    | 在当前会话（连接）中有效    | `SET @a = 5;`          |
| **系统变量** | `@@变量名`   | 全局或会话系统配置       | `SELECT @@autocommit;` |

---

## 🧠 二、局部变量（Local Variables）

### ✅ 定义语法

只能在 **存储过程 / 函数内部** 定义：

```sql
DECLARE 变量名 数据类型 [DEFAULT 默认值];
```

示例：

```sql
DECLARE num INT DEFAULT 0;
DECLARE username VARCHAR(50);
DECLARE total DECIMAL(10,2) DEFAULT 100.0;
```

> ⚠️ `DECLARE` 必须写在 `BEGIN ... END` 块的开头，不能夹杂在 SQL 中间。

---

### ✅ 赋值方式

```sql
SET 变量名 = 表达式;
```

或用 `SELECT ... INTO ...`：

```sql
SELECT COUNT(*) INTO num FROM user;
```

---

### 🧩 完整示例：

```sql
DELIMITER //

CREATE PROCEDURE count_user()
BEGIN
    DECLARE total INT DEFAULT 0;
    SELECT COUNT(*) INTO total FROM user;
    SELECT CONCAT('用户总数: ', total);
END //

DELIMITER ;
```

---

## 🧰 三、用户变量（User Variables）

在任何 SQL 会话中都可以使用，以 `@` 开头：

```sql
SET @x = 10;
SET @y = @x + 5;
SELECT @y;    -- 输出 15
```

也可以用 SELECT 赋值：

```sql
SELECT COUNT(*) INTO @cnt FROM user;
```

📘 特点：

* 不需要事先定义；
* 数据类型自动根据赋值推断；
* 作用域是“**当前连接会话**”；
* 存储过程外也可以使用。

---

## ⚙️ 四、SELECT INTO 与 SET 的区别

| 用法            | 示例                                  | 说明           |
| ------------- | ----------------------------------- | ------------ |
| `SET`         | `SET a = 1;`                        | 一般赋值语句，简单表达式 |
| `SELECT INTO` | `SELECT COUNT(*) INTO a FROM user;` | 从查询结果中取值赋给变量 |

📌 注意：
`SELECT ... INTO` 只能用于 **单行结果集**（否则会出错）。

---

## 🔁 五、示例：循环累加

```sql
DELIMITER //

CREATE PROCEDURE loop_sum(IN n INT, OUT total INT)
BEGIN
    DECLARE i INT DEFAULT 1;
    DECLARE s INT DEFAULT 0;

    WHILE i <= n DO
        SET s = s + i;
        SET i = i + 1;
    END WHILE;

    SET total = s;
END //

DELIMITER ;
```

调用：

```sql
CALL loop_sum(5, @result);
SELECT @result;   -- 输出 15
```

---

## 📋 六、变量命名与类型提示

✅ 命名建议：

* 不以数字开头；
* 避免与表名、字段名相同；
* 可用下划线分隔（如 `total_sum`）。

✅ 常用数据类型：

| 类型                  | 示例                             | 用途   |
| ------------------- | ------------------------------ | ---- |
| `INT`               | `DECLARE i INT;`               | 整数计数 |
| `DECIMAL(10,2)`     | `DECLARE money DECIMAL(10,2);` | 金额   |
| `VARCHAR(n)`        | `DECLARE name VARCHAR(50);`    | 字符串  |
| `DATE` / `DATETIME` | `DECLARE reg_date DATE;`       | 日期时间 |

---

## 🔍 七、综合示例：判断并更新余额

```sql
DELIMITER //

CREATE PROCEDURE update_balance(IN uid INT, IN amt DECIMAL(10,2), OUT result VARCHAR(50))
BEGIN
    DECLARE current_balance DECIMAL(10,2) DEFAULT 0.0;

    -- 取出当前余额
    SELECT balance INTO current_balance FROM account WHERE id = uid;

    -- 判断余额
    IF current_balance + amt < 0 THEN
        SET result = '余额不足';
    ELSE
        UPDATE account SET balance = balance + amt WHERE id = uid;
        SET result = CONCAT('交易成功，新余额：', current_balance + amt);
    END IF;
END //

DELIMITER ;
```

调用：

```sql
CALL update_balance(1, -500, @msg);
SELECT @msg;
```

---

是否希望我帮你整理一张「变量使用速查表」📄（包括 DECLARE / SET / SELECT INTO / INOUT 等全对照表）？
那样你可以快速查用法和区别，一看就明白。
