# Lecture 4: Higher-Order Functions

> 高阶函数：接受另一个函数作为参数的函数

## 函数作为形参和返回值

### 形参

在具有多个形参的函数A中，某些形参可能是函数B。在调用函数A时，一定会自动执行函数B。例如：

```python
def if_(c, t, f):
    if c:
        return t
    else:
        return f

from math import sqrt

def real_sqrt(x):
    """Return the real part of the square root of x."""
    return if_(x >= 0, sqrt(x), 0)
```

在执行`return if_(x >= 0, sqrt(x), 0)`时，函数`if_`会被调用，并且参数`t`会被求值为`sqrt(x)`。这意味着如果`x`是负数，`sqrt(x)`仍然会被计算，从而导致报错。

### 返回值

```python
def make_adder(n):
    """Return a function that takes one argument k and returns k + n."""
    def adder(k):
        return k + n
    return adder
```

想计算4+3，可以调用makeadder(3)(4)

## 有先后顺序的布尔运算

and和or运算符具有短路特性，即它们会根据第一个操作数的值决定是否计算第二个操作数。使用and时，如果第一个操作数为False，则整个表达式为False，第二个操作数不会被计算。使用or时，如果第一个操作数为True，则整个表达式为True，第二个操作数不会被计算。

## 断言语句

断言语句用于在程序中插入检查点，以确保某个条件为真。如果条件为假，程序会抛出一个AssertionError异常。例如：

```python
assert 2 > 3, "This is wrong!"  # 抛出异常 AssertionError: This is wrong!
```