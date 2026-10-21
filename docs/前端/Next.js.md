# Next.js

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

Server Component的意思是，这段代码只在服务器上运行一次，生成好 HTML 之后直接发给浏览器，浏览器收到的是已经画好的成品，不需要再自己跑这段代码。

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

但是，服务器只能生成静态的 HTML，它没法处理用户交互。比如点击、实时联想等，都必须要在浏览器里实时运行代码才能做到，服务器生成一次 HTML 之后是没法响应后续点击的。这时候就需要`use client`，如果某个组件需要上面这些交互功能，你就在这个文件最顶部写一行`use client`，告诉 Next.js 这个组件是 Client Component，它需要在浏览器里运行。

## 数据获取（跨域）

页面显示出来之后，通常还需要**动态数据**，比如：

- 文章列表（可能会更新，不能写死在 HTML 里）
- 点赞数、评论数（实时变化）
- 用户登录状态

页背后的 JS 代码**主动发一个请求**去问服务器要数据，这个动作术语叫 **API 请求**（也叫 "fetch 数据"、"调接口"）。浏览器有个安全规则：**网页所在的域名/端口，和它请求的接口的域名/端口，如果不一样，浏览器默认会拦截这个请求**，防止恶意网站偷偷读取你在别的网站上的数据。

```typescript
async rewrites() {
  return [
    {
      source: "/api/:path*",
      destination: "http://localhost:8000/api/:path*",
    },
  ];
}
```

流程图：

```
浏览器  --请求 /api/v1/articles-->  Next.js服务器(3000)
                                        |
                                        | Next.js 在服务器内部转发
                                        ↓
                                  后端服务器(8000)
                                        |
                                        | 返回文章数据
                                        ↓
                                  Next.js服务器
                                        |
                                        | 把数据原样传回
                                        ↓
浏览器  <--拿到数据，没有跨域问题-------
```

关键点：**浏览器全程只跟 3000 端口打交道，完全不知道背后还转发去了 8000 端口**。转发这一步是在服务器之间悄悄完成的，服务器和服务器之间互相请求，不受浏览器跨域规则限制（跨域限制只针对浏览器）。

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
