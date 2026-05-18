# HTML

## 基本结构

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>页面标题</title>
</head>
<body>
    <h1>标题</h1>
    <p>段落</p>
</body>
</html>
```

## 常用标签

- `<div>` 块级容器，用于布局分组。
- `<span>` 行内容器，用于包裹行内文本。
- `<a href="url">` 超链接，`target="_blank"` 在新标签页打开。
- `<img src="url" alt="描述">` 图片，`alt` 为必须属性。
- `<ul>` / `<ol>` / `<li>` 无序列表 / 有序列表 / 列表项。
- `<table>` / `<tr>` / `<td>` / `<th>` 表格相关标签。
- `<form>` 表单容器，配合 `<input>`、`<select>`、`<textarea>` 使用。
- `<button>` 按钮，`type` 可选 `submit`、`reset`、`button`。

## 语义化标签

HTML5 引入了语义化标签，提升可读性与可访问性：

- `<header>` 页头
- `<nav>` 导航
- `<main>` 主要内容
- `<article>` 独立内容块
- `<section>` 内容分区
- `<aside>` 侧边栏
- `<footer>` 页脚

## 表单与输入

```html
<form action="/submit" method="POST">
    <label for="name">姓名</label>
    <input type="text" id="name" name="name" required>
    
    <input type="email" placeholder="邮箱">
    <input type="password" placeholder="密码">
    <input type="number" min="0" max="100">
    <input type="checkbox" id="agree">
    <input type="radio" name="gender" value="male">
    
    <select name="city">
        <option value="hz">杭州</option>
        <option value="sh">上海</option>
    </select>
    
    <textarea rows="4" cols="50"></textarea>
    <button type="submit">提交</button>
</form>
```

## 全局属性

- `id` 唯一标识。
- `class` CSS 类名，可多个。
- `style` 行内样式。
- `title` 鼠标悬停提示。
- `hidden` 隐藏元素。
- `data-*` 自定义数据属性。
- `contenteditable` 使元素可编辑。

## 无障碍 (a11y)

- 图片加有意义的 `alt` 描述。
- 表单控件用 `<label>` 关联。
- 使用语义化标签而非纯 `div`。
- `aria-*` 属性补充语义。
