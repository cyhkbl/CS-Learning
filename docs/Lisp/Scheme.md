# Scheme

## 表达式 (Expressions)

Scheme 程序完全由**表达式**组成，表达式主要分为以下几类：

*   **自求值表达式 (Self-evaluating expressions)**：直接代表其本身的值，如数字 (`2`, `3.3`) 和布尔值 (`true`).
*   **符号 (Symbols)**：代表操作符或变量名，如 `+`, `-`, `quotient`, `not`.
*   **调用表达式 (Call expressions)**：应用函数的表达式，例如 `(quotient 10 2)` 或 `(f x)`.
*   **特殊形式 (Special forms)**：具有特殊求值规则的表达式，不是普通的函数调用。例如 `(if a b c)`, `(let ((x 2)) (+ x 1))`, `define`, `lambda`, `cond`, `begin` 等.

**求值环境 (Environment)**：
*   表达式在特定的环境（Environment）中求值，环境赋予符号意义.
*   **帧 (Frame)**：存在全局帧（Global frame，每个人都知道的操作如 `multiply`）和局部帧（Local frame，特定上下文的含义）。局部帧的定义优先于全局帧.

#### 2. 函数定义与调用
*   **定义 (Define)**：使用 `define` 关键字。
    *   语法：`(define (<函数名> <参数>) (<函数体>))`
    *   Python 对比：
        *   Python: `def sum_squares(x, y): return x * x + y * y`
        *   Scheme: `(define (sum-squares x y) (+ (* x x) (* y y)))`
*   **返回值**：Scheme 没有 `return` 语句。调用表达式的值就是过程体中**最后一个**表达式的值.
*   **调用语法**：括号在最外面。例如 `(sum-squares 3 4)`.

#### 3. 控制流 (Control Flow)
Scheme 使用嵌套的表达式而非 Python 那样的语句块来实现逻辑控制。

*   **If 语句**：
    *   语法：`(if <条件> <真值结果> <假值结果>)`
    *   示例 (斐波那契数列)：
        `(define (fib n) (if (or (= n 0) (= n 1)) n (+ (fib (- n 2)) (fib (- n 1)))))`.
*   **Cond 语句 (多重条件)**：
    *   类似于 Python 的 `if-elif-else`。
    *   语法：
        ```scheme
        (cond ((<条件1>) <结果1>)
              ((<条件2>) <结果2>)
              (else <默认结果>))
        ```
    *   示例：`(cond ((> x 10) 'big) ((> x 5) 'medium) (else 'small))`.
*   **Begin 语句**：
    *   用于将多个表达式组合成一个表达式（通常用于在 `if` 或 `cond` 分支中执行多个操作，如打印）。
    *   语法：`(begin (exp1) (exp2))`.

#### 4. 迭代与递归 (Iteration via Recursion)
*   **无循环语句**：Scheme 没有 `for` 或 `while` 循环语句.
*   **必须使用递归**：所有的迭代必须通过递归实现。
*   **内部辅助函数**：通常会在函数内部定义一个辅助函数（helper function）来通过参数传递状态（如计数器 `k` 和累加器 `total`）。
    *   示例 (求前 n 项和)：
        ```scheme
        (define (sum-first-n n)
          (define (f k total)
            (if (> k n)
                total
                (f (+ k 1) (+ total k))))
          (f 1 0))
        ```
       

#### 5. 高级特性
*   **Lambda 表达式**：
    *   计算结果为匿名过程（anonymous procedures）。
    *   语法：`(lambda (<形参>) <函数体>)`
    *   等价写法：`(define (plus4 x) (+ x 4))` 等同于 `(define plus4 (lambda (x) (+ x 4)))`.
    *   **直接调用**：操作符本身也可以是一个调用表达式。
        示例：`((lambda (x y z) (+ x y (square z))) 1 2 3)`.
*   **Let 表达式**：
    *   用于建立临时的局部变量绑定，仅在当前表达式内有效。
    *   语法：
        ```scheme
        (let ((<变量1> <值1>)
              (<变量2> <值2>))
          <使用变量的函数体>)
        ```
    *   注意：一旦离开 `let` 的范围，变量绑定即失效.
*   **高阶函数技巧**：
    *   函数本身可以作为值传递。
    *   示例 `a-plus-abs-b`：通过判断 `b` 的正负，决定操作符是 `+` 还是 `-`，然后将其应用。
    *   代码：`(define (a-plus-abs-b a b) ((if (< b 0) - +) a b))`.

#### 6. Python 与 Scheme 快速对比表
| 特性 | Python | Scheme |
| :--- | :--- | :--- |
| **调用** | `f(x)` | `(f x)` - 括号在外 |
| **返回** | `return value` | 最后一个表达式的值 |
| **逻辑运算** | `f(3) and f(4)` (短路) | `(and (f 3) (f 4))` |
| **循环** | `for`, `while` | 递归 (Recursion) |
| **多重分支** | `if...elif...else` | `(cond ...)` |
| **变量赋值** | `a = 3` | `(define a 3)` 或 `(let ((a 3)) ...)` |