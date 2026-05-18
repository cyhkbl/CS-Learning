# CSS

## 选择器

- `*` 通配符选择器。
- `#id` ID 选择器。
- `.class` 类选择器。
- `element` 标签选择器。
- `div, p` 分组选择器。
- `div p` 后代选择器。
- `div > p` 子代选择器。
- `div + p` 相邻兄弟选择器。
- `div ~ p` 通用兄弟选择器。
- `input[type="text"]` 属性选择器。
- `p::first-line` 伪元素选择器。
- `a:hover` 伪类选择器。

## 盒模型

每个元素都是一个盒子，由内到外：

- `content` 内容区域，由 `width` 和 `height` 控制。
- `padding` 内边距，内容与边框之间的空间。
- `border` 边框。
- `margin` 外边距，元素与其他元素之间的空间。

```css
/* 标准盒模型 vs IE盒模型 */
box-sizing: content-box; /* 默认，width 只含 content */
box-sizing: border-box;  /* width 含 content + padding + border */
```

## Flexbox 布局

```css
.container {
    display: flex;
    flex-direction: row;        /* row | column | row-reverse | column-reverse */
    flex-wrap: wrap;            /* nowrap | wrap | wrap-reverse */
    justify-content: center;    /* flex-start | flex-end | center | space-between | space-around | space-evenly */
    align-items: center;        /* stretch | flex-start | flex-end | center | baseline */
    gap: 10px;
}

.item {
    flex-grow: 1;    /* 放大比例 */
    flex-shrink: 0;  /* 缩小比例 */
    flex-basis: auto; /* 初始大小 */
    /* 简写: flex: 1 0 auto; */
    align-self: flex-end; /* 单独对齐 */
}
```

## Grid 布局

```css
.grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    grid-template-rows: auto;
    gap: 16px;
}

.item {
    grid-column: 1 / 3;  /* 占两列 */
    grid-row: 1 / 2;
}
```

## 定位

- `static` 默认，文档流。
- `relative` 相对自身偏移，不脱离文档流。
- `absolute` 相对最近的定位祖先，脱离文档流。
- `fixed` 相对视口，脱离文档流。
- `sticky` 粘性定位，滚动到阈值后变为 fixed。

## 响应式

```css
/* 移动优先 */
@media (min-width: 768px) { /* 平板 */ }
@media (min-width: 1024px) { /* 桌面 */ }
@media (prefers-color-scheme: dark) { /* 暗色模式 */ }
```

## 变量与函数

```css
:root {
    --primary: #2563eb;
    --spacing: 8px;
}

.button {
    color: var(--primary);
    padding: calc(var(--spacing) * 2);
}
```

## 常用属性速查

- `display`: `none | block | inline | inline-block | flex | grid`。
- `overflow`: `visible | hidden | scroll | auto`。
- `z-index`: 层级，需配合定位使用。
- `opacity`: 透明度 0~1。
- `transition`: 过渡动画。
- `transform`: 变换 `translate` `rotate` `scale`。
- `box-shadow`: 阴影。
- `border-radius`: 圆角。

## 特异性与层叠

CSS = Cascading Style Sheets，层叠规则：

1. `!important` > 内联 > ID > 类/属性/伪类 > 标签/伪元素。
2. 同特异性后声明覆盖前声明。
3. 继承的属性特异性最低。
