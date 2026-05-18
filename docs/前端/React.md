# React

## 核心概念

React 是一个用于构建用户界面的 JavaScript 库，核心理念：

- **组件化** UI 拆分为独立、可复用的组件。
- **单向数据流** 数据从父组件流向子组件。
- **声明式** 描述 UI 应该是什么样子，而非怎么做。
- **Virtual DOM** 通过 diff 算法最小化真实 DOM 操作。

## JSX

JSX 是 JavaScript 的语法扩展，编译为 `React.createElement` 调用。

```jsx
const element = <h1 className="title">Hello, {name}!</h1>;

// 条件渲染
{isLoggedIn ? <UserPanel /> : <LoginPage />}

// 列表渲染
{items.map(item => <li key={item.id}>{item.name}</li>)}
```

注意：`class` 要写成 `className`，`for` 写成 `htmlFor`。

## Hooks

### useState

```jsx
const [count, setCount] = useState(0);

// 函数式更新（依赖前一个值时使用）
setCount(prev => prev + 1);
```

### useEffect

```jsx
useEffect(() => {
    // 副作用：API 请求、订阅、手动 DOM 操作
    const controller = new AbortController();
    fetchData(controller.signal);

    return () => {
        // 清理函数：取消订阅、取消请求
        controller.abort();
    };
}, [dependencies]); // 依赖数组变化时执行
```

- 空依赖数组 `[]`：仅挂载时执行一次。
- 无依赖数组：每次渲染都执行。
- 有依赖：依赖变化时执行。

### useContext

```jsx
const ThemeContext = createContext('light');

// 提供
<ThemeContext.Provider value="dark">
    <App />
</ThemeContext.Provider>

// 消费
const theme = useContext(ThemeContext);
```

### useRef

```jsx
const inputRef = useRef(null);
<input ref={inputRef} />
<inputRef.current.focus();

// 保存不触发渲染的值
const timerRef = useRef(null);
```

### useMemo / useCallback

```jsx
// 缓存计算结果
const expensive = useMemo(() => computeExpensive(a, b), [a, b]);

// 缓存函数引用
const handleClick = useCallback(() => doSomething(id), [id]);
```

### useReducer

```jsx
function reducer(state, action) {
    switch (action.type) {
        case 'increment': return { count: state.count + 1 };
        case 'decrement': return { count: state.count - 1 };
        default: return state;
    }
}

const [state, dispatch] = useReducer(reducer, { count: 0 });
```

## 组件模式

### 受控组件 vs 非受控组件

```jsx
// 受控：React 管理状态
<input value={value} onChange={e => setValue(e.target.value)} />

// 非受控：DOM 自己管理
<input ref={inputRef} defaultValue="hello" />
```

### 组合模式 (Composition)

```jsx
function Card({ children, title }) {
    return (
        <div className="card">
            <h2>{title}</h2>
            {children}
        </div>
    );
}
```

### 高阶组件 (HOC)

```jsx
function withAuth(WrappedComponent) {
    return function(props) {
        const user = useAuth();
        if (!user) return <Redirect to="/login" />;
        return <WrappedComponent {...props} user={user} />;
    };
}
```

## React Router (v6+)

```jsx
<BrowserRouter>
    <Routes>
        <Route path="/" element={<Layout />}>
            <Route index element={<Home />} />
            <Route path="about" element={<About />} />
            <Route path="users/:id" element={<User />} />
        </Route>
    </Routes>
</BrowserRouter>

// 编程式导航
const navigate = useNavigate();
navigate('/about');

// 获取参数
const { id } = useParams();
const [searchParams] = useSearchParams();
```

## 状态管理

- **useState + useContext** 小型应用够用。
- **Zustand** 轻量级，API 简洁，无 boilerplate。
- **Redux Toolkit** 大型应用，有完善的 devtools。
- **Jotai** 原子化状态管理。
- **TanStack Query** 服务端状态管理（缓存、轮询、失效）。

## 性能优化

- `React.memo` 避免不必要的重渲染。
- `useMemo` / `useCallback` 缓存计算和函数。
- 虚拟列表（`react-window`、`@tanstack/virtual`）处理长列表。
- 代码分割 `React.lazy` + `Suspense`。
- 避免在渲染中创建新对象/数组。
