这个问题问得好 👍，涉及 **表单上传字段名** 和 **Spring Controller 参数绑定规则**。

------

## 1. 文件上传字段名和参数名是否必须一致？

是的，**必须保持一致**，Spring 才能自动绑定。

### 例子

前端表单：

```html
<form action="/upload" method="post" enctype="multipart/form-data">
    <input type="file" name="file">
    <button type="submit">上传</button>
</form>
```

Controller：

```java
@PostMapping("/upload")
public String upload(@RequestParam("file") MultipartFile file) {
    System.out.println(file.getOriginalFilename());
    return "success";
}
```

👉 这里 `input name="file"` 必须和 `@RequestParam("file")` 对应。

------

## 2. 如果名字不一样怎么办？

有两种办法：

### 方法1：用 `@RequestParam("xxx")` 指定前端字段名

```java
@PostMapping("/upload")
public String upload(@RequestParam("myFile") MultipartFile file) {
    System.out.println(file.getOriginalFilename());
    return "success";
}
```

对应前端：

```html
<input type="file" name="myFile">
```

### 方法2：参数名和表单字段名保持一致

```java
@PostMapping("/upload")
public String upload(MultipartFile file) {
    ...
}
```

对应前端：

```html
<input type="file" name="file">
```

------

## 3. 多文件上传

```html
<input type="file" name="files" multiple>
@PostMapping("/upload")
public String upload(@RequestParam("files") MultipartFile[] files) {
    for (MultipartFile file : files) {
        System.out.println(file.getOriginalFilename());
    }
    return "success";
}
```

------

## 4. 小结

- **字段名必须对应**，否则绑定不到。
- 不一致时 → 用 `@RequestParam("表单字段名")` 明确指定。
- 多文件上传时，表单字段名可以是同一个（例如 `files`），对应 `MultipartFile[]` 或 `List<MultipartFile>`。

------

要不要我帮你整理一个 **表单字段名 vs Spring 参数绑定对照表**（单值、多值、文件、对象绑定全覆盖），这样你能一眼看懂什么时候必须对齐？