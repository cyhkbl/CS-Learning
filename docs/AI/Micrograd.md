# Micrograd

micrograd 是一个"自动微分引擎"（autograd engine）——你给它算一个数学表达式，它帮你算梯度。它操作的是标量（单个数字），不是矩阵。所以一个神经元被拆成很多个小小的加法和乘法。虽然简单，但能搭出完整的神经网络做分类。

定义了一个包装类。普通数字 3.0 你没法追踪它怎么来的，但 Value(3.0) 知道：
- 自己的值是多少（data）
- 自己的梯度是多少（grad，一开始是 0）
- 自己是怎么来的（_prev 记录父节点，_op 记录运算符）
- 自己的反向传播逻辑是什么（_backward，一开始是空的）

```python
def init(self, data, _children=(), _op=''):
    self.data = data        # 存实际数值
    self.grad = 0           # 存梯度（导数）
    self._backward = lambda: None   # 反向传播函数
    self._prev = set(_children)      # 记住"这个值是谁算出来的"
    self._op = _op                  # 记住"用什么运算算出来的"
```