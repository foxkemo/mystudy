非常好 👍
MySQL **存储过程（Stored Procedure）** 是一种在数据库端封装 SQL 逻辑的机制，能让你像调用函数一样执行一组 SQL 操作，常用于：

* 封装复杂的业务逻辑
* 减少客户端与数据库之间的交互次数
* 提高性能和代码复用性

---

## 🧩 一、基本语法结构

```sql
DELIMITER //

CREATE PROCEDURE 存储过程名 (参数列表)
BEGIN
    -- SQL 语句
END //

DELIMITER ;
```

> `DELIMITER //` 是为了临时修改语句结束符（因为默认的 `;` 会导致定义过程提前结束）

---

## 🧠 二、参数类型

| 类型      | 说明       |
| ------- | -------- |
| `IN`    | 输入参数（默认） |
| `OUT`   | 输出参数     |
| `INOUT` | 输入输出参数   |

示例：

```sql
CREATE PROCEDURE test_proc(IN a INT, IN b INT, OUT sum INT)
BEGIN
    SET sum = a + b;
END;
```

调用方式：

```sql
CALL test_proc(10, 20, @result);
SELECT @result;
```

---

## ⚙️ 三、带流程控制的存储过程

### 1️⃣ IF 条件判断

```sql
CREATE PROCEDURE check_age(IN age INT)
BEGIN
    IF age >= 18 THEN
        SELECT '成年人';
    ELSE
        SELECT '未成年';
    END IF;
END;
```

---

### 2️⃣ WHILE 循环

```sql
CREATE PROCEDURE loop_sum(IN n INT, OUT total INT)
BEGIN
    DECLARE i INT DEFAULT 1;
    SET total = 0;
    WHILE i <= n DO
        SET total = total + i;
        SET i = i + 1;
    END WHILE;
END;
```

---

### 3️⃣ CASE 分支

```sql
CREATE PROCEDURE grade(IN score INT)
BEGIN
    CASE
        WHEN score >= 90 THEN SELECT '优秀';
        WHEN score >= 60 THEN SELECT '及格';
        ELSE SELECT '不及格';
    END CASE;
END;
```

---

## 📦 四、带查询与插入的存储过程

### 例1：查询用户信息

```sql
CREATE PROCEDURE get_user(IN uid INT)
BEGIN
    SELECT * FROM user WHERE id = uid;
END;
```

### 例2：插入记录

```sql
CREATE PROCEDURE add_user(IN name VARCHAR(50), IN email VARCHAR(100))
BEGIN
    INSERT INTO user(username, email) VALUES(name, email);
END;
```

---

## 🧰 五、查看与删除

* 查看所有存储过程：

  ```sql
  SHOW PROCEDURE STATUS;
  ```

* 查看某个过程定义：

  ```sql
  SHOW CREATE PROCEDURE proc_name;
  ```

* 删除存储过程：

  ```sql
  DROP PROCEDURE IF EXISTS proc_name;
  ```

---

## 💡 六、存储过程与事务结合使用

```sql
CREATE PROCEDURE transfer(IN from_id INT, IN to_id INT, IN amount DECIMAL(10,2))
BEGIN
    DECLARE EXIT HANDLER FOR SQLEXCEPTION
    BEGIN
        ROLLBACK;
    END;
    
    START TRANSACTION;
        UPDATE account SET balance = balance - amount WHERE id = from_id;
        UPDATE account SET balance = balance + amount WHERE id = to_id;
    COMMIT;
END;
```

这样即使中途出错也能安全回滚。

---

## 🔍 七、调用方式总结

| 调用场景     | 示例                                         |
| -------- | ------------------------------------------ |
| 无参过程     | `CALL proc_name();`                        |
| 有输入参数    | `CALL proc_name(123, 'abc');`              |
| 有输出参数    | `CALL proc_name(1, @x); SELECT @x;`        |
| INOUT 参数 | `SET @a=5; CALL proc_name(@a); SELECT @a;` |

---

是否希望我帮你写一个**实际项目风格的示例**（比如“用户注册过程”、“订单统计过程”）？
那样我可以展示 **变量声明 + 事务 + 判断 + 插入/更新** 的完整示例。
