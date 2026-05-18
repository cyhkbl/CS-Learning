# Next.js

## 简介

Next.js 是基于 React 的全栈框架，由 Vercel 开发。核心特性：

- 文件系统路由
- 服务端渲染 (SSR) / 静态生成 (SSG)
- API Routes
- 自动代码分割
- 图片优化
- App Router（基于 React Server Components）

## App Router (v13+)

目录结构即路由：

```
app/
├── layout.tsx        # 根布局
├── page.tsx          # 首页 /
├── about/
│   └── page.tsx      # /about
├── blog/
│   ├── page.tsx      # /blog
│   └── [slug]/
│       └── page.tsx  # /blog/:slug
└── api/
    └── users/
        └── route.ts  # API: /api/users
```

## 页面与布局

```tsx
// app/page.tsx — Server Component（默认）
export default function Home() {
    return <h1>首页</h1>;
}

// app/layout.tsx — 根布局
export default function RootLayout({ children }: { children: React.ReactNode }) {
    return (
        <html lang="zh-CN">
            <body>{children}</body>
        </html>
    );
}
```

## Server Components vs Client Components

```tsx
// Server Component（默认）— 可直接访问数据库、文件系统
async function UserList() {
    const users = await db.user.findMany();
    return <ul>{users.map(u => <li key={u.id}>{u.name}</li>)}</ul>;
}

// Client Component — 需要 "use client" 指令
"use client";
import { useState } from "react";

export function Counter() {
    const [count, setCount] = useState(0);
    return <button onClick={() => setCount(c => c + 1)}>{count}</button>;
}
```

规则：Server Component 可以包含 Client Component，反之不行（Client Component 的子组件自动变为 Client）。

## 数据获取

```tsx
// Server Component 中直接 fetch（自动缓存）
async function Posts() {
    const res = await fetch('https://api.example.com/posts', {
        next: { revalidate: 3600 } // ISR: 每小时重新生成
    });
    const posts = await res.json();
    return <PostList posts={posts} />;
}

// Server Action（表单提交）
"use server";
async function createPost(formData: FormData) {
    const title = formData.get("title") as string;
    await db.post.create({ data: { title } });
}
```

## 动态路由与路由组

```tsx
// 动态路由: app/blog/[slug]/page.tsx
export default function BlogPost({ params }: { params: { slug: string } }) {
    return <h1>{params.slug}</h1>;
}

// 路由组: app/(marketing)/page.tsx — 不影响 URL
// 平行路由: app/@auth/login/page.tsx
// 拦截路由: app/(..)photo/[id]/page.tsx
```

## 中间件

```ts
// middleware.ts（项目根目录）
import { NextResponse } from "next/server";
import type { NextRequest } from "next/server";

export function middleware(request: NextRequest) {
    const token = request.cookies.get("token")?.value;
    if (!token && request.nextUrl.pathname.startsWith("/dashboard")) {
        return NextResponse.redirect(new URL("/login", request.url));
    }
}

export const config = {
    matcher: ["/dashboard/:path*"],
};
```

## 常用配置 (next.config.js)

```js
/** @type {import('next').NextConfig} */
const nextConfig = {
    images: {
        remotePatterns: [{ hostname: "example.com" }],
    },
    experimental: {
        serverActions: { enabled: true },
    },
};

module.exports = nextConfig;
```

## 部署

- **Vercel** 零配置，Git push 自动部署。
- **Docker** `next build` 后用 `next start`。
- **静态导出** `output: "export"` 生成纯静态站点。
