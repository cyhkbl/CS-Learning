# TypeScript

## 基本类型

```ts
let name: string = "hello";
let age: number = 20;
let active: boolean = true;
let data: null = null;
let value: undefined = undefined;

// 数组
let nums: number[] = [1, 2, 3];
let strs: Array<string> = ["a", "b"];

// 元组
let pair: [string, number] = ["age", 20];

// 枚举
enum Direction { Up, Down, Left, Right }
let dir: Direction = Direction.Up;

// any / unknown / never / void
let x: any;        // 任意类型，关闭检查
let y: unknown;    // 安全的 any，使用前需收窄
function err(): never { throw new Error(); }  // 永不返回
function log(): void { console.log("hi"); }   // 无返回值
```

## 接口与类型别名

```ts
// interface
interface User {
    id: number;
    name: string;
    email?: string;           // 可选
    readonly createdAt: Date; // 只读
}

// type
type Status = "pending" | "active" | "done";
type Point = { x: number; y: number };

// 扩展
interface Admin extends User {
    permissions: string[];
}

// 交叉类型
type AdminUser = User & { permissions: string[] };
```

`interface` 与 `type` 的区别：`interface` 可声明合并（同名自动合并），`type` 更灵活（联合、交叉、映射类型）。

## 泛型

```ts
// 泛型函数
function identity<T>(arg: T): T {
    return arg;
}

// 泛型约束
function getLength<T extends { length: number }>(arg: T): number {
    return arg.length;
}

// 泛型接口
interface ApiResponse<T> {
    code: number;
    data: T;
    message: string;
}

// 泛型工具类型
type Partial<T> = { [P in keyof T]?: T[P] };
type Pick<T, K extends keyof T> = { [P in K]: T[P] };
type Omit<T, K extends keyof T> = Pick<T, Exclude<keyof T, K>>;
type Record<K extends keyof any, T> = { [P in K]: T };
```

## 类型收窄

```ts
// typeof
function format(value: string | number) {
    if (typeof value === "string") {
        return value.toUpperCase(); // TS 知道这里是 string
    }
    return value.toFixed(2); // number
}

// in
interface Dog { bark(): void; }
interface Cat { meow(): void; }
function speak(animal: Dog | Cat) {
    if ("bark" in animal) animal.bark();
}

// 自定义类型守卫
function isDog(animal: Dog | Cat): animal is Dog {
    return "bark" in animal;
}
```

## 常用工具类型

```ts
Partial<User>        // 所有属性可选
Required<User>       // 所有属性必填
Readonly<User>       // 所有属性只读
Pick<User, 'id' | 'name'>  // 选取部分属性
Omit<User, 'email'>        // 排除部分属性
Record<string, number>     // 键值对类型
ReturnType<typeof fn>      // 函数返回类型
Parameters<typeof fn>      // 函数参数类型
NonNullable<string | null> // 排除 null/undefined
Awaited<Promise<string>>   // 解包 Promise
```

## 模块与声明

```ts
// 导出
export interface Config { ... }
export default class App { ... }

// 声明文件 (.d.ts)
declare module "some-lib" {
    export function doSomething(): void;
}

// 全局声明
declare global {
    interface Window {
        __APP_CONFIG__: Config;
    }
}
```

## 配置要点 (tsconfig.json)

- `strict: true` 开启所有严格检查。
- `target` 编译目标 ES 版本。
- `module` 模块系统（`ESNext`、`CommonJS`）。
- `paths` 路径别名。
- `outDir` 输出目录。
- `esModuleInterop` 兼容 CommonJS 导入。
