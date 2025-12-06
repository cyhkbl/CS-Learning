# Lecture 3: Control

程序中，存在全局框架和局部框架。按照缩进区分框架，从而在不同框架中可以使用相同的变量名。

return语句可以返回多个值。例如：

```python
>>> def divide_exact(n, d):
...    return n // d, n % d
>>> quotient, remainder = divide_exact(2013, 10)
```

# Python中的真假值

False values in Python: False，0，''，None

True values in Python: Anything else