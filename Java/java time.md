好的 👍 我帮你系统梳理一下 **Java 时间日期类** 和 **System 类中与时间相关的常用方法**。

---

## 1. Java 时间日期类体系

Java 中的时间日期类有两大阶段：

### （1）旧版 API（Java 1.0 \~ 1.7 常用）

* **`java.util.Date`**

  * 代表日期和时间（精确到毫秒）。
  * 常用方法：

    * `getTime()`：获取毫秒时间戳（1970-01-01 00:00:00 UTC 起的毫秒数）。
    * `after(Date when)` / `before(Date when)`：比较时间先后。
    * `toString()`：输出可读的字符串形式。

* **`java.util.Calendar`**

  * 提供对日期的字段操作（年、月、日、时、分、秒等）。
  * 常用方法：

    * `get(int field)`：获取指定字段（如 `Calendar.YEAR`）。
    * `set(int field, int value)`：设置某个字段。
    * `add(int field, int amount)`：对某个字段加/减。
    * `getTime()`：转换为 `Date`。

* **`java.text.SimpleDateFormat`**

  * 日期格式化与解析。
  * 常用方法：

    * `format(Date date)`：`Date -> String`
    * `parse(String source)`：`String -> Date`
    * 模式字符串：`yyyy-MM-dd HH:mm:ss` 等。

---

### （2）新版 API（Java 8 引入 `java.time` 包，推荐使用）

> 更加现代、线程安全，替代 Date/Calendar。

* **`LocalDate`**：仅日期（年-月-日）。

  * `LocalDate.now()`：获取当前日期。
  * `plusDays(n)` / `minusMonths(n)`：日期加减。
  * `getYear()` / `getMonthValue()` / `getDayOfMonth()`：取字段。

* **`LocalTime`**：仅时间（时:分:秒.纳秒）。

  * `LocalTime.now()`：获取当前时间。
  * `plusHours(n)` / `minusMinutes(n)`：时间加减。

* **`LocalDateTime`**：日期 + 时间。

  * `LocalDateTime.now()`：当前日期时间。
  * `plusDays()`、`minusWeeks()` 等。

* **`ZonedDateTime`**：带时区的日期时间。

  * `ZonedDateTime.now(ZoneId.of("Asia/Shanghai"))`

* **`Instant`**：时间戳（类似旧 API 的 `Date`）。

  * `Instant.now()`：当前 UTC 时间戳。
  * `toEpochMilli()`：转毫秒数。

* **`Duration` / `Period`**：时间间隔（前者以秒/纳秒计，后者以年月日计）。

  * `Duration.between(time1, time2)`
  * `Period.between(date1, date2)`

* **`DateTimeFormatter`**：日期格式化与解析（替代 `SimpleDateFormat`）。

  * `format()`：格式化。
  * `parse()`：解析。
  * 内置常量：`DateTimeFormatter.ISO_DATE`、`ISO_DATE_TIME` 等。

---

## 2. `System` 类下与时间相关的方法

`System` 是一个工具类（静态类），提供一些底层方法，跟 **时间测量** 密切相关的有：

* **`currentTimeMillis()`**

  * 返回当前时间的毫秒时间戳（long 类型）。
  * 常用于计算耗时、日志时间戳。

  ```java
  long start = System.currentTimeMillis();
  // 代码块
  long end = System.currentTimeMillis();
  System.out.println("耗时: " + (end - start) + "ms");
  ```

* **`nanoTime()`**

  * 返回相对时间，单位纳秒（不是绝对时间戳，而是单调递增值）。
  * 更适合精确计算程序耗时。

  ```java
  long start = System.nanoTime();
  // 代码块
  long end = System.nanoTime();
  System.out.println("耗时: " + (end - start) + "ns");
  ```

* **`exit(int status)`**

  * 虽然不是直接和时间有关，但与程序生命周期组织相关。

---

## 3. 对比总结

| 功能     | 旧版 (`Date/Calendar`)           | 新版 (`java.time`)              |
| -------- | -------------------------------- | ------------------------------- |
| 线程安全 | ❌ 否                             | ✅ 是                            |
| 可读性   | 一般                             | 好（API 清晰）                  |
| 时区支持 | 有限                             | 强大                            |
| 格式化   | `SimpleDateFormat`（非线程安全） | `DateTimeFormatter`（线程安全） |

**System 类** 提供的是 **低层时间戳工具**（毫秒/纳秒），而 **java.time** 是 **高层日期时间 API**（更适合业务逻辑）。

---

要不要我给你整理一个 **常用 Java 时间/日期 API 的速查表（含代码示例）**？这样你以后写项目就能直接查。