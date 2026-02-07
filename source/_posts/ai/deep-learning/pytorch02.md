---
title: 从零实现线性回归
date: 2023/06/18
categories:
- [人工智能, 深度学习]
tags: 深度学习
cover: assets/pexels-05.jpg
---

# 从零实现线性回归

+++info 阅读提示
本文包含两部分：先从“线性回归的概念与训练目标”出发，再用 PyTorch 分别演示“从零实现（手写 SGD、损失函数、数据迭代器）”与“用 `nn.Linear` 的简洁实现”。建议边看边跑代码，并在每个阶段打印 loss 与参数误差，建立直觉。
+++

> 在尝试囫囵吞枣学习深度学习后，我深切地认识到了自己的浮躁：耗费了不少时间，却只学到寥寥无几的零散知识。

## 回归（Regression）

回归是研究一组随机变量 \(Y_1, Y_2, \dots, Y_i\) 与另一组变量 \(X_1, X_2, \dots, X_k\) 之间关系的统计分析方法。它用于为一个或多个自变量与因变量之间的关系建模。在自然科学和社会科学领域，回归常用来刻画输入与输出的关系；当我们需要预测**连续数值**时，通常就会遇到回归问题。

## 线性回归

线性回归基于几个简单假设：

- 自变量与因变量的关系是线性的，即因变量 \(y\) 可以表示为自变量 \(x\) 中元素的加权和（通常包含观测噪声）。
- 噪声相对“正常”，一般假设服从正态分布。

书中举的经典例子是房价预测：根据房龄与房间面积估算房屋价值。我们需要收集真实数据集（训练集）。训练集中每行数据称为**样本**（数据点），如房屋属性（房龄、面积）。预测目标（房价）称为**标签（label）/目标（target）**，自变量（房龄、面积）称为**特征（feature）**。

线性假设是指目标（房价）可以表示为特征（面积与房龄）的加权和：

**price = w_area · area + w_age · age + b**

其中 \(w\) 是权重，决定每个特征对预测值的影响；\(b\) 称为偏置（bias）或截距，当特征全为 0 时决定预测值基线。

给定训练数据特征 **X** 和对应标签 **y**，线性回归的目标是找到权重向量 **w** 与偏置 \(b\)。在寻找最好的模型参数之前，还需要两个要素：

- 一种模型质量的度量方式（损失函数）
- 一种更新模型以提高预测质量的方法（模型优化）

## 损失函数

> 损失函数（loss function）能够量化目标的实际值与预测值之间的差距。通常选择非负数作为损失，数值越小表示预测越好；完美预测时损失为 0。回归问题中常用平方误差作为损失。

单个样本的平方误差（除以 2 便于求导）可写为 \(\frac{(y\_hat - y)^2}{2}\)。度量整个训练集质量时，通常计算 \(n\) 个样本损失的均值。

```python
def squared_loss(y_hat, y):
    # y_hat：预测值
    return (y_hat - y.reshape(y_hat.shape)) ** 2 / 2
````

## 模型优化

解析解：线性回归有解析解（可用公式直接算出最优解），但并不是所有问题都存在解析解。解析解便于数学分析，但限制严格，无法覆盖深度学习里大量模型。

在无法得到解析解的情况下，仍然可以有效训练模型。书中使用的是梯度下降（尤其是小批量随机梯度下降 SGD），几乎可以优化绝大多数深度学习模型。

基本步骤：

* 初始化模型参数（如随机初始化）
* 从数据集中随机抽取小批量样本
* 沿负梯度方向更新参数，重复迭代

```python
def sgd(params, lr, batch_size):
    with torch.no_grad():
        for param in params:
            param -= lr * param.grad / batch_size
            param.grad.zero_()
```

## 线性回归从零开始实现

### 数据集生成

构造人造数据集（合成数据）：

```python
import random
import torch
from d2l import torch as d2l

def syn(w, b, num_examples):
    x = torch.normal(0, 1, (num_examples, len(w)))      # 特征：均值 0 方差 1
    y = torch.matmul(x, w) + b                           # 真实标签
    y += torch.normal(0, 0.01, y.shape)                  # 加入噪声
    return x, y.reshape((-1, 1))

true_w = torch.tensor([2, -3.4])
true_b = 4.2
features, labels = syn(true_w, true_b, 1000)
```

### 读取数据集

批量读取样本（手写 DataLoader）：

```python
def data_iter(batch_size, features, labels):
    num_examples = len(features)
    indices = list(range(num_examples))
    random.shuffle(indices)
    for i in range(0, num_examples, batch_size):
        batch_indices = torch.tensor(indices[i:min(i + batch_size, num_examples)])
        yield features[batch_indices], labels[batch_indices]
```

### 初始化模型参数

```python
w = torch.normal(0, 0.01, size=(2, 1), requires_grad=True)
b = torch.zeros(1, requires_grad=True)
```

### 定义模型

```python
def linreg(X, w, b):
    return torch.matmul(X, w) + b
```

### 定义损失函数

```python
def squared_loss(y_hat, y):
    return (y_hat - y.reshape(y_hat.shape)) ** 2 / 2
```

### 定义优化算法

```python
def sgd(params, lr, batch_size):
    with torch.no_grad():
        for param in params:
            param -= lr * param.grad / batch_size
            param.grad.zero_()
```

### 开始训练

```python
lr = 0.03
num_epochs = 3
net = linreg
loss = squared_loss
batch_size = 10

for epoch in range(num_epochs):
    for X, y in data_iter(batch_size, features, labels):
        l = loss(net(X, w, b), y)
        l.sum().backward()
        sgd([w, b], lr, batch_size)
    with torch.no_grad():
        train_l = loss(net(features, w, b), labels)
        print(f"epoch {epoch + 1}, loss {float(train_l.mean()):f}")

print(f"w 的估计误差: {true_w - w.reshape(true_w.shape)}")
print(f"b 的估计误差: {true_b - b}")
```

可以看到，训练得到的参数与真实参数已经非常接近。

## 利用 PyTorch 简洁实现

利用 PyTorch 提供的 `nn.Linear` 来实现线性回归：

```python
import torch
from torch.utils import data
from torch import nn
from d2l import torch as d2l

true_w = torch.tensor([2, -3.4])
true_b = 4.2

features, labels = d2l.synthetic_data(true_w, true_b, 1000)

def load_array(data_arrays, batch_size, is_train=True):
    dataset = data.TensorDataset(*data_arrays)
    return data.DataLoader(dataset, batch_size, shuffle=is_train)

batch_size = 10
data_iter = load_array((features, labels), batch_size)

net = nn.Sequential(nn.Linear(2, 1))
net[0].weight.data.normal_(0, 0.01)
net[0].bias.data.fill_(0)

loss = nn.MSELoss()
trainer = torch.optim.SGD(net.parameters(), lr=0.03)

num_epochs = 3
for epoch in range(num_epochs):
    for X, y in data_iter:
        l = loss(net(X), y)
        trainer.zero_grad()
        l.backward()
        trainer.step()
    l = loss(net(features), labels)
    print(f"epoch {epoch + 1}, loss {l:f}")
```

{% links %}

- site: "d2l.ai：线性回归"
  owner: "Dive into Deep Learning"
  url: "[https://d2l.ai/](https://d2l.ai/)"
  desc: "《动手学深度学习》线性回归与从零实现相关章节"
  image: "assets/pexels-11.jpg"
  color: "#e9546b"
- site: "PyTorch Linear 文档"
  owner: "PyTorch"
  url: "[https://pytorch.org/docs/stable/generated/torch.nn.Linear.html](https://pytorch.org/docs/stable/generated/torch.nn.Linear.html)"
  desc: "nn.Linear 的参数与用法说明"
  image: "assets/pexels-11.jpg"
  color: "#4e7cff"
{% endlinks %}
