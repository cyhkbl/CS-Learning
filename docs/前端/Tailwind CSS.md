# Tailwind CSS

## 简介

Tailwind CSS 是一个 utility-first 的 CSS 框架，通过原子化的工具类直接在 HTML 中编写样式，无需手写 CSS 文件。

## 基本用法

```html
<!-- 传统方式 -->
<button class="bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700 transition">
    点击
</button>

<!-- Flex 布局 -->
<div class="flex items-center justify-between gap-4">
    <span class="text-lg font-bold">标题</span>
    <span class="text-gray-500 text-sm">副标题</span>
</div>
```

## 响应式设计

断点前缀：

- `sm:` ≥ 640px
- `md:` ≥ 768px
- `lg:` ≥ 1024px
- `xl:` ≥ 1280px
- `2xl:` ≥ 1536px

```html
<div class="w-full md:w-1/2 lg:w-1/3">
    <!-- 移动端全宽，平板半宽，桌面三分之一 -->
</div>
```

## 常用工具类

### 排版

- `text-sm` / `text-base` / `text-lg` / `text-xl` 字号。
- `font-bold` / `font-semibold` / `font-medium` 字重。
- `text-gray-500` / `text-blue-600` 颜色。
- `text-center` / `text-left` 对齐。
- `leading-relaxed` 行高。
- `tracking-wide` 字间距。

### 间距

- `p-4` padding 1rem，`px-4` 水平，`py-4` 垂直。
- `m-4` margin，`mx-auto` 水平居中。
- `gap-4` flex/grid 子元素间距。

### 颜色

- `bg-white` / `bg-gray-100` / `bg-blue-600` 背景色。
- `border-gray-200` 边框色。
- `ring-2 ring-blue-500` 聚焦环。
- `opacity-50` 透明度。

### 尺寸

- `w-full` / `w-1/2` / `w-screen` 宽度。
- `h-screen` / `h-auto` 高度。
- `max-w-sm` / `max-w-4xl` 最大宽度。
- `min-h-screen` 最小高度。

### 布局

- `flex` / `grid` / `block` / `hidden` / `inline-flex`。
- `items-center` / `justify-between`。
- `grid-cols-3` / `col-span-2`。
- `absolute` / `relative` / `fixed` / `sticky`。
- `z-10` / `z-50`。

## 状态修饰符

```html
<button class="bg-blue-600 hover:bg-blue-700 focus:ring-2 active:bg-blue-800 disabled:opacity-50">
    按钮
</button>

<!-- 暗色模式 -->
<div class="bg-white dark:bg-gray-900 text-gray-900 dark:text-white">
    内容
</div>
```

## 自定义配置 (tailwind.config.js)

```js
/** @type {import('tailwindcss').Config} */
export default {
    content: ["./src/**/*.{html,tsx}"],
    darkMode: "class",
    theme: {
        extend: {
            colors: {
                primary: "#2563eb",
            },
            fontFamily: {
                sans: ["Inter", "sans-serif"],
            },
        },
    },
    plugins: [],
};
```

## @apply 复用

在 CSS 文件中复用工具类：

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer components {
    .btn-primary {
        @apply bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700 transition;
    }
}
```

## 与 CSS-in-JS 对比

- Tailwind：零运行时，样式在构建时生成，性能好。
- CSS-in-JS（styled-components、emotion）：运行时注入，灵活但有性能开销。
- 推荐：新项目用 Tailwind，已有 CSS-in-JS 项目按需迁移。
