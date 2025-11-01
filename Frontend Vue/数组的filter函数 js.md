`filter()` 是 JavaScript 数组中非常常用的**高阶函数**，用于根据条件**过滤数组元素**。
它不会修改原数组，而是返回一个**新数组**，包含所有满足条件的元素。

---

### ✅ 基本语法

```js
array.filter(function(element, index, array) {
  // 返回 true 表示保留该元素，false 表示丢弃
})
```

或使用箭头函数更简洁：

```js
array.filter(item => 条件表达式)
```

---

### 💡 示例 1：过滤出大于 10 的数

```js
const numbers = [5, 12, 8, 130, 44];
const result = numbers.filter(num => num > 10);
console.log(result);  // [12, 130, 44]
```

---

### 💡 示例 2：过滤对象数组

```js
const users = [
  { name: 'Alice', age: 25 },
  { name: 'Bob', age: 17 },
  { name: 'Charlie', age: 30 }
];

const adults = users.filter(user => user.age >= 18);
console.log(adults);
// 输出：[{ name: 'Alice', age: 25 }, { name: 'Charlie', age: 30 }]
```

---

### 💡 示例 3：结合字符串搜索

```js
const fruits = ['apple', 'banana', 'grape', 'orange'];
const result = fruits.filter(fruit => fruit.includes('a'));
console.log(result);  // ['apple', 'banana', 'grape', 'orange']
```

---

### 💡 示例 4：与 Vue 一起使用（常见）

```html
<div v-for="item in items.filter(i => i.done)" :key="item.id">
  {{ item.text }}
</div>
```

上例表示：只显示 `done == true` 的条目。

---

### ⚙️ 参数说明

| 参数名       | 含义      |
| --------- | ------- |
| `element` | 当前遍历的元素 |
| `index`   | 当前索引    |
| `array`   | 原数组本身   |

---

是否要我帮你写一个带 `filter()` 的 Vue 小示例，比如在输入框里动态过滤列表？
