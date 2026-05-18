# JavaScript

## 变量声明

```js
var a = 1;    // 函数作用域，有变量提升，可重复声明
let b = 2;    // 块级作用域，无提升，不可重复声明
const c = 3;  // 块级作用域，声明时必须初始化，不可重新赋值
```

优先使用 `const`，需要重新赋值时用 `let`，不要用 `var`。

## 数据类型

基本类型（不可变）：

- `string`、`number`、`boolean`、`null`、`undefined`、`symbol`、`bigint`。

引用类型（可变）：

- `object`、`array`、`function`。

```js
typeof "hello"    // "string"
typeof 42         // "number"
typeof null       // "object" （历史遗留 bug）
Array.isArray([]) // true
```

## 解构赋值

```js
// 对象解构
const { name, age, city = '杭州' } = person;

// 数组解构
const [first, , third] = [1, 2, 3];

// 函数参数解构
function greet({ name, greeting = '你好' }) {
    return `${greeting}, ${name}`;
}

// 嵌套解构
const { address: { city } } = person;
```

## 展开运算符

```js
// 数组
const arr2 = [...arr1, 4, 5];

// 对象
const obj2 = { ...obj1, newKey: 'value' };

// 函数参数
function sum(...nums) {
    return nums.reduce((a, b) => a + b, 0);
}
```

## Promise 与 async/await

```js
// Promise
fetch('/api/data')
    .then(res => res.json())
    .then(data => console.log(data))
    .catch(err => console.error(err));

// async/await
async function getData() {
    try {
        const res = await fetch('/api/data');
        const data = await res.json();
        return data;
    } catch (err) {
        console.error(err);
    }
}

// 并发
const [users, posts] = await Promise.all([
    fetch('/api/users'),
    fetch('/api/posts')
]);
```

## 数组常用方法

```js
// 链式调用
const result = users
    .filter(u => u.age >= 18)
    .map(u => u.name)
    .sort()
    .join(', ');

// reduce 聚合
const total = items.reduce((sum, item) => sum + item.price, 0);

// find / findIndex / some / every
const user = users.find(u => u.id === 1);
const hasAdmin = users.some(u => u.role === 'admin');
const allActive = users.every(u => u.active);
```

## 对象操作

```js
// 可选链
const city = user?.address?.city;

// 空值合并
const name = input ?? '默认值'; // null/undefined 时取默认

// Object 方法
Object.keys(obj)    // 键数组
Object.values(obj)  // 值数组
Object.entries(obj) // [key, value] 数组
Object.assign(target, ...sources) // 合并对象
Object.fromEntries(entries)       // 从键值对创建对象
```

## 模块系统

```js
// ES Modules（推荐）
import { foo } from './module.js';
import defaultExport from './module.js';
export const bar = 42;
export default function() {}

// 动态导入
const module = await import('./module.js');
```

## 闭包

```js
function createCounter() {
    let count = 0;
    return {
        increment: () => ++count,
        getCount: () => count
    };
}

const counter = createCounter();
counter.increment();
counter.getCount(); // 1
```

闭包 = 函数 + 其词法作用域。内部函数保持对外部变量的引用。

## this

- 全局上下文：`window`（严格模式下 `undefined`）。
- 对象方法：指向调用对象。
- 箭头函数：继承外层 `this`，无法改变。
- `call` / `apply` / `bind`：手动绑定 `this`。
