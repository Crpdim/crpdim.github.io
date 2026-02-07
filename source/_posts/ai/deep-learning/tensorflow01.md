---
title: TensorFlow
date: 2023/04/04
categories:
- [人工智能, 深度学习]
tags: 深度学习
cover: assets/pexels-11.jpg
---

# TensorFlow

+++info 阅读提示
本文内容包含较多 TensorFlow 1.x 的概念与写法（Graph / Session）。TensorFlow 2.x 默认启用 Eager Execution，更推荐使用 `tf.function()` 来构建静态图；`tf.compat.v1.Session()` 虽仍可用，但一般不建议在新项目里继续依赖。
+++

## TensorFlow 介绍

[TensorFlow]{.rainbow} 是由 Google Brain 团队基于 Google 内部第一代深度学习系统 DistBelief 改进而来的通用计算框架。DistBelief 是 Google 于 2011 年开发的内部深度学习工具，并在公司内部取得了巨大成功。虽然 DistBelief 已被多个内部产品使用，但由于其过度依赖内部系统架构，难以对外开源，因此 Google Brain 对其进行改进，并在 2015 年正式发布基于 Apache 2.0 协议开源的 TensorFlow。相比 DistBelief，TensorFlow 的计算模型更通用、计算更快、支持平台更多、覆盖的深度学习算法更广，并且系统稳定性更高。

## 环境搭建

TensorFlow 的构建与生态会涉及多个工具链（例如 Protocol Buffer、Bazel 等）。不过对大多数使用者来说，**直接安装预编译版本**是最省事的方式。

### 安装

+++ 默认默认 Conda 环境安装
```bash
conda install -c anaconda tensorflow
````

+++

+++info Apple Silicon（M1/M2）安装说明
如果没有使用 conda 环境，按下面步骤一般也能安装成功；但如果你使用的是 Anaconda，建议换成 miniforge（避免踩 “Anaconda 对 Apple Silicon 支持不完整” 的坑）。
+++

**M1 安装（示例流程）**

1. 下载 tensorflow_macos（示例版本）

   * 访问：`https://github.com/apple/tensorflow_macos/releases`
   * 选择：`tensorflow_macos-0.1alpha3.tar.gz`

2. 下载 miniforge（arm64）

   * 访问：`https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-MacOSX-arm64.sh`

3. 安装 miniforge（安装后重启终端）

```bash
/bin/bash ./Miniforge3-MacOSX-arm64.sh
```

4. 创建并激活虚拟环境

```bash
conda create -n tensorflow_env python=3.8
conda activate tensorflow_env
# conda deactivate 退出虚拟环境
```

5. 查看 Python 路径（确认当前环境）

```bash
which python
```

6. 安装 tensorflow（示例：解压 + 依赖安装 + whl 安装）

```bash
tar -zxvf tensorflow_macos-0.1alpha3.tar.gz -C /Users/username/Local/Envs

libs="/Users/username/Local/Envs/tensorflow_macos/arm64"   # username 为当前用户目录名
env="/Users/username/miniforge3/envs/tensorflow_env"       # username 为当前用户目录名

pip install --upgrade pip wheel setuptools cached-property six
pip install --upgrade -t "$env/lib/python3.8/site-packages/" --no-dependencies --force "$libs/grpcio-1.33.2-cp38-cp38-macosx_11_0_arm64.whl"
pip install --upgrade -t "$env/lib/python3.8/site-packages/" --no-dependencies --force "$libs/h5py-2.10.0-cp38-cp38-macosx_11_0_arm64.whl"
pip install --upgrade -t "$env/lib/python3.8/site-packages/" --no-dependencies --force "$libs/numpy-1.18.5-cp38-cp38-macosx_11_0_arm64.whl"
pip install --upgrade -t "$env/lib/python3.8/site-packages/" --no-dependencies --force "$libs/tensorflow_addons_macos-0.1a3-cp38-cp38-macosx_11_0_arm64.whl"
pip install absl-py astunparse flatbuffers gast google_pasta keras_preprocessing opt_einsum protobuf tensorflow_estimator termcolor typing_extensions wrapt wheel tensorboard typeguard
pip install --upgrade -t "$env/lib/python3.8/site-packages/" --no-dependencies --force "$libs/tensorflow_macos-0.1a3-cp38-cp38-macosx_11_0_arm64.whl"
```

安装完成后，进入对应环境测试：

```python
import tensorflow
```

## 入门

### 计算图（Graph）

> 计算图是 TensorFlow 最基本的概念之一：它**定义计算**，但**不直接计算**任何东西，也**不包含实际数值**。TensorFlow 中的计算会被转化为图上的节点（算子）以及边（张量流动）。

TensorFlow 中，Tensor 是张量，Flow 是流：张量可以理解为多维数组，流表达了张量之间通过计算相互转化的过程。

+++info 直观理解
“图”描述的是：**我要怎么计算**；而真正“跑起来拿到数值”，需要执行机制（TF1.x 的 Session，TF2.x 的 eager / tf.function）。
+++

### 张量（Tensor）

> 张量是 TensorFlow 管理数据的形式。

在 TensorFlow 中，数据以张量形式表示：零阶张量表示标量（scalar），一阶张量为向量（vector），n 阶张量可以理解为 n 维数组。需要注意的是：**张量在 TensorFlow 中更像“计算结果的引用”**，它并不一定直接保存具体数值，而是保存“如何得到这些数值”的计算关系（尤其在静态图时代更明显）。

### 会话（Session）

> 会话（Session）拥有并管理 TensorFlow 程序运行时的资源，它允许执行计算图（或其中一部分）。会话会分配资源（可能在一台或多台机器上）并维护中间结果与变量的实际值。

TensorFlow 1.x 中常见的会话模式有两种：显式创建/关闭会话，或使用上下文管理器（这里给出显式方式）：

```python
# 创建一个会话
sess = tf.Session()
# 执行计算图中的某个张量/算子
sess.run(...)
# 关闭会话，释放资源
sess.close()
```

下面对比 TF1.x 与 TF2.x 的 Hello World：

**TF1.x：**

```python
import tensorflow as tf
msg = tf.constant('Hello, TensorFlow!')
sess = tf.Session()
print(sess.run(msg))
```

**TF2.x：**

```python
import tensorflow as tf
msg = tf.constant('Hello, TensorFlow!')
tf.print(msg)
```


{% links %}
- site: "TensorFlow 官方文档"
  owner: "TensorFlow"
  url: "https://www.tensorflow.org/"
  desc: "安装、教程与 API 文档"
  image: "/Fly1.jpg"
  color: "#e9546b"
- site: "Apple tensorflow_macos（历史方案）"
  owner: "Apple"
  url: "https://github.com/apple/tensorflow_macos/releases"
  desc: "早期 Apple Silicon 上的 TF 发行包（示例流程用到）"
  image: "/Fly1.jpg"
  color: "#4e7cff"
- site: "Miniforge（conda-forge）"
  owner: "conda-forge"
  url: "https://github.com/conda-forge/miniforge"
  desc: "更适合 Apple Silicon 的 conda 发行版"
  image: "/Fly1.jpg"
  color: "#2bb673"
{% endlinks %}
