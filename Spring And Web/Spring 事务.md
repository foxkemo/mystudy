Spring 事务 @Transactional(rollbackFor= Exception.class)

默认是 RuntimeException

START TRANSACTION;

UPDATE account SET balance = balance - 100 WHERE name = '小明';
UPDATE account SET balance = balance + 100 WHERE name = '小红';

COMMIT;   -- 提交，保存结果
-- 或者
ROLLBACK; -- 回滚，撤销操作

好问题 👍 你现在问的是 Spring 事务里 **`propagation` 属性**，这是 `@Transactional` 的一个核心参数。它定义了 **事务传播行为** —— 当一个事务方法调用另一个事务方法时，Spring 应该怎么处理事务。

---

## 1. 为什么需要 propagation？

默认情况下，调用一个带事务的方法时，会创建或加入一个事务。但有时候：

* 你可能希望 **复用当前事务**（保证原子性）。
* 也可能希望 **新建事务**（互不影响）。
* 甚至希望 **不使用事务**。

这就是 `propagation` 的作用。

---

## 2. propagation 的取值（7 种常用）

| 传播行为             | 说明                                                         |
| -------------------- | ------------------------------------------------------------ |
| **REQUIRED**（默认） | 如果当前有事务，就加入；没有就新建一个。常见场景。           |
| **REQUIRES\_NEW**    | 总是新建一个事务，暂停当前事务。外层事务和它互不影响。       |
| **SUPPORTS**         | 有事务就加入，没有就以非事务方式运行。                       |
| **NOT\_SUPPORTED**   | 总是以非事务方式运行，如果当前有事务，就挂起。               |
| **NEVER**            | 必须在没有事务的情况下运行，否则抛异常。                     |
| **MANDATORY**        | 必须在事务中运行，如果没有事务就抛异常。                     |
| **NESTED**           | 如果当前有事务，就在里面再开启一个“嵌套事务点”（可以单独回滚到 savepoint）；如果没有，就新建一个。 |

---

## 3. 举例说明

```java
@Service
public class UserService {
    @Transactional(propagation = Propagation.REQUIRED)
    public void addUser() {
        // A 方法
        accountService.addAccount(); // 调用另一个事务方法
    }
}

@Service
public class AccountService {
    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void addAccount() {
        // B 方法
    }
}
```

执行 `addUser()` 时：

* `addUser()` 开启事务 A。
* 调用 `addAccount()` 时，因 `REQUIRES_NEW`，暂停事务 A，开启新事务 B。
* 如果 B 回滚，不影响 A。

---

## 4. 场景对比

* **REQUIRED（默认）**：
  所有调用者共用一个事务，适合保证整体一致性。
* **REQUIRES\_NEW**：
  适合日志记录、消息发送等，即使主事务失败，也要提交成功。
* **NESTED**：
  适合部分子任务回滚，但不影响主事务。

---

## 5. 注意事项

* **NESTED 和 REQUIRES\_NEW 区别**：

  * `REQUIRES_NEW` 是完全独立的事务；
  * `NESTED` 是在当前事务里创建一个保存点（依赖底层数据库是否支持 Savepoint）。
* 如果数据库/事务管理器不支持 `NESTED`，可能会退化为 `REQUIRED`。
* 默认 `rollbackFor = RuntimeException.class`，即运行时异常才触发回滚。

---

✅ 总结：

* **`propagation` = 事务传播行为**，控制方法间事务的嵌套与复用。
* 常用：`REQUIRED`（默认）、`REQUIRES_NEW`、`NESTED`。
* 选择规则：

  * 要么一起成功/失败 → `REQUIRED`
  * 要么互不影响 → `REQUIRES_NEW`
  * 要么部分可回滚 → `NESTED`

---

要不要我帮你画一张 **事务传播场景图**（父事务调用子事务，不同 propagation 下的提交/回滚效果对比）？