# Lecture 2: Functions

对于变量，修改赋值语句中其他变量的值，变量赋值后值始终不变；对于函数，修改其他变量的值，调用函数后函数的返回值可能会受到影响。

有重名时，先检查本地帧，再检查全局帧。例如：

```python
def square(square):
    return square * square
x=square(3)  # 返回 9，这里把3传给了局部变量square，再调用square()函数
```

当一个内置函数名称与变量名称冲突时，优先使用变量。例如：

```python
def max(x, y):
    if x > y:
        return x
    return y

max = 42
print(max(1, 2))  # 输出 42
```

内置函数也可以作为另一个内置函数的变量名。例如：`max=min`，调用max(1,2)时会调用min函数。

python在赋值时，从右往左先查询值，再赋值。

解释器不会将 None 作为表达式的值显示出来。当函数不返回任何东西时，默认返回 None。对于`print()`函数，输出需要打印在屏幕上的字符后，返回 None。例如：

```python
>>> print(print(1),print(2))
1
2
None None
```

print 将显示不带引号的文本，但是 return 将保留引号。

Traceback 中，最近一次调用位于底部。

## doctest

doctest 可以快速为你的代码编写测试。

```python
def square(x):
    """
    >>> square(2)
    4
    >>> square(3)
    9
    """
    return x * x
```

要运行doctest，输入：`python3 -m doctest file.py`

命令行工具有一个 -v 选项，代表详细（verbose）。

`python3 -m doctest file.py -v` 除了告诉您哪些文档测试失败之外，还会告诉您哪些文档测试通过了。

## 交互式调试

通过交互方式使用 Python：`python3 -i file.py`

## print与 return 输出时的区别

在输出字符串时，print() 输出内容，不带引号。
交互式环境自动显示表达式结果，用单引号包裹字符串。