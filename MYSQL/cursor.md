在 **MySQL 存储过程/函数** 中，**游标（Cursor）** 用于 **逐行处理查询结果集**，因为存储过程里不能直接像程序语言那样循环处理 SELECT 的多行结果。下面我帮你系统讲解。

---

# 1️⃣ 游标概念

* **作用**：逐行访问 SELECT 查询返回的多行记录。
* **适用场景**：

  * 需要对每一行数据做复杂处理。
  * 无法一次性用 SQL 完成计算时。
* **生命周期**：

  1. **声明（DECLARE cursor_name CURSOR FOR select_statement）**
  2. **打开（OPEN cursor_name）**
  3. **取数据（FETCH cursor_name INTO var_list）**
  4. **关闭（CLOSE cursor_name）**

---

# 2️⃣ 游标语法示例

```sql
DELIMITER //

CREATE PROCEDURE process_orders()
BEGIN
    DECLARE done INT DEFAULT 0;
    DECLARE order_id INT;
    DECLARE order_cursor CURSOR FOR
        SELECT id FROM orders WHERE status='pending';
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = 1;

    OPEN order_cursor;

    read_loop: LOOP
        FETCH order_cursor INTO order_id;
        IF done THEN
            LEAVE read_loop;
        END IF;
        -- 处理每一行
        UPDATE orders SET status='processed' WHERE id=order_id;
    END LOOP;

    CLOSE order_cursor;
END //

DELIMITER ;
```

---

# 3️⃣ 关键点解释

| 关键字                                               | 作用                |
| ------------------------------------------------- | ----------------- |
| `DECLARE cursor_name CURSOR FOR select_statement` | 声明游标              |
| `OPEN cursor_name`                                | 打开游标，准备读取数据       |
| `FETCH cursor_name INTO var_list`                 | 读取当前行数据到变量        |
| `CLOSE cursor_name`                               | 关闭游标，释放资源         |
| `CONTINUE HANDLER FOR NOT FOUND`                  | 当游标取完数据时设置标志，避免报错 |

---

# 4️⃣ 使用注意事项

1. 游标 **只能在存储过程/函数内使用**，不能在普通 SQL 查询里直接使用。
2. **必须先声明变量**（`DECLARE`）用于接收 FETCH 的列值。
3. 处理完成后**一定要关闭游标**，否则会占用资源。
4. 可以嵌套游标，但嵌套时要用不同变量和循环标签。

---

# 5️⃣ 示例：遍历用户表，累加余额

```sql
DELIMITER //

CREATE PROCEDURE sum_active_balances(OUT total_bal INT)
BEGIN
    DECLARE done INT DEFAULT 0;
    DECLARE bal DECIMAL(10,2);
    DECLARE total DECIMAL(10,2) DEFAULT 0;
    DECLARE cur CURSOR FOR SELECT balance FROM users WHERE status='active';
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = 1;

    OPEN cur;

    LOOP
        FETCH cur INTO bal;
        IF done THEN
            LEAVE;
        END IF;
        SET total = total + bal;
    END LOOP;

    CLOSE cur;

    SET total_bal = total;
END //

DELIMITER ;
```

调用：

```sql
CALL sum_active_balances(@sum);
SELECT @sum;
```

---

如果你愿意，我可以帮你画一张 **游标处理流程图**，从声明到关闭，每一步直观可视化。

你希望我画吗？
