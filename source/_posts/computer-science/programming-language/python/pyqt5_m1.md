---
title: M1安装pyqt5
date: 2022/09/20 20:46:25
categories:
- [计算机科学, 编程语言, Python]
tags: Python
cover: assets/pexels-02.jpg

---

# M1安装pyqt5

> 在m1上无法使用pip安装pyqt5，总结一下安装经验

## 1. 用Homebrew安装 qt、sip、PyQt5

### 终端中输入

```bash
brew install qt
brew install sip
brew install pyqt5
```

### 检查安装情况：

```bash
brew list
```

## 2. 找到刚安装的pyqt5的库文件

```bash
# 库文件所在位置
/opt/homebrew/Cellar/pyqt@5/5.15.7_2/lib/python3.9/site-packages
```

## 3. 将site-packages中的文件复制到python环境

```bash
#我使用miniforge创建的python虚拟环境所在位置
/Users/crpdim/miniforge3/envs/MyEnv/lib/python3.9/site-packages
使用cp命令将pyqt5库文件复制到python环境
cp -r /opt/homebrew/Cellar/pyqt@5/5.15.7_2/lib/python3.9/site-packages/. /opt/homebrew/Cellar/pyqt@5/5.15.7_2/lib/python3.9/site-packages
```

## 4. 检查是否能正常使用

`import PyQt5`没发生异常则表示能正常使用

```bash
(MyEnv) crpdim@MyComputer ~ %python
Python 3.9.16 (main, Mar  8 2023, 04:29:24) 
[Clang 14.0.6 ] :: Anaconda, Inc. on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import PyQt5
>>>
```



