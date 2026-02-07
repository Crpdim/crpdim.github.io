---
title: PyTorch 求导
date: 2023/06/13
categories:
- [人工智能, 深度学习]
tags: 深度学习
cover: assets/pexels-16.jpg
---

# PyTorch 求导

+++info 阅读提示
本文聚焦 PyTorch 的自动微分（Autograd）机制与常见用法：标量反向传播、梯度累计与清零，以及非标量输出如何通过 `sum()`（或传入 `gradient` 参数）来完成反传。
+++

> 导数的计算几乎是所有深度学习优化算法的关键步骤。在深度学习中，通常会选择模型参数可微的损失函数：也就是对每个参数增加或减少一个无穷小量时，可以知道损失会以多大的速度增加或减少。

偏导：在深度学习中，函数通常依赖许多变量，因此需要将微分思想推广到多元函数上，即求某变量的偏导。  
梯度：将多元函数的所有偏导数组合起来，可以得到该函数的梯度向量。

## 自动微分

> 深度学习框架通过自动计算导数（自动微分）加快求导。

### 举个例子：对函数 y = 2xᵀx 关于列向量 x 求导

```python
import torch
x = torch.arange(4.0)
# x = tensor([0., 1., 2., 3.])
```

这里创建变量 x 并赋初值。要计算函数 y 关于 x 的梯度，需要一块区域存放梯度；这样就不需要对每个参数求导时重复分配内存。

```python
x.requires_grad_(True)  # 等价于 x = torch.arange(4.0, requires_grad=True)
# y 关于 x 的梯度会存放在 x.grad 中
```

计算函数 y：

```python
y = 2 * torch.dot(x, x)  # y = 2 *（x 的内积）
# y = tensor(28., grad_fn=<MulBackward0>)
```

通过反向传播得到 y 关于变量 x 每个分量的梯度：

```python
y.backward()  # 反向传播
print(x.grad)  # 打印 y 关于 x 每个分量的梯度
# tensor([ 0.,  4.,  8., 12.])
```

函数 y = 2xᵀx 关于 x 的梯度应为 4x，而 4 * x = tensor([ 0.,  4.,  8., 12.])，因此梯度计算正确。

默认情况下，PyTorch 会**累计**梯度，因此如果需要继续计算关于 x 的另一个函数，需先清空之前的梯度：

```python
x.grad.zero_()  # 清空梯度
# 此时就可以计算其他函数关于 x 的梯度
```

### 对非标量变量的反向传播：y = x * x

当 y 不是标量时，y 关于 x 的导数是一个矩阵。调用向量的反向传播时，我们常见目标并不是显式构造雅可比矩阵，而是得到“各分量梯度的聚合结果”（例如对 batch 中每个样本损失求和后再反传）。

```python
y = x * x  # y 不是标量
y.sum().backward()
print(x.grad)
# tensor([0., 2., 4., 6.])
```

{% links %}

- site: "PyTorch Autograd 官方文档"
  owner: "PyTorch"
  url: "[https://pytorch.org/docs/stable/autograd.html](https://pytorch.org/docs/stable/autograd.html)"
  desc: "自动微分机制与 backward 用法说明"
  image: "assets/pexels-11.jpg"
  color: "#e9546b"
- site: "PyTorch 张量与梯度教程"
  owner: "PyTorch"
  url: "[https://pytorch.org/tutorials/beginner/basics/autogradqs_tutorial.html](https://pytorch.org/tutorials/beginner/basics/autogradqs_tutorial.html)"
  desc: "入门教程：requires_grad、grad、反向传播"
  image: "assets/pexels-11.jpg"
  color: "#4e7cff"
  {% endlinks %}
