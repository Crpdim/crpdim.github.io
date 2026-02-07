---
title: C++ 数值转换：有符号 / 无符号的坑
date: 2023/07/30
categories:
- [计算机科学, 编程语言, C++]
tags: C++
cover: assets/pexels-11.jpg
---

# C++ 数值转换：有符号 / 无符号的坑

+++info 阅读提示
这篇记录一个刷题时非常典型的坑：`vector::size()` 返回 `size_t`（无符号），当它与有符号整数混合运算时，可能发生“有符号转无符号”，导致结果变成一个巨大的正数，从而让循环条件意外成立。
+++

> 昨天刷题的时候，遇到了一个奇怪的问题。

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> array;
    array.push_back(10);   // array.size() = 1
    int num = 4;
    for (int i = 1; i < array.size() - num; ++i) {
        cout << "hello world" << "\n";
    }
    return 0;
}
````

主要的问题在这段代码：在 `for` 循环里使用 `i < array.size() - num` 来判断能否进入循环。此时 `i = 1`，`array.size() = 1`，`num = 4`。按直觉 `1 - 4` 应该是负数，因此循环不应该进入，但程序却能进入循环。

后来发现：我对无符号数和有符号数的运算规则了解不够。

`array.size()` 的返回类型是 `size_t`（无符号），当它与有符号的 `int num` 混合运算时，会触发**整型提升与转换规则**：有符号数会被当成无符号数来参与运算，于是 `1 - 4` 不是负数，而是“无符号下的回绕（wrap around）”，最终得到一个非常大的正数。

## 原理

C++ 中有符号数和无符号数进行运算（包括算术运算和比较）时，通常会把有符号数转换为无符号数再计算；并且表达式的结果类型往往也会是无符号类型。

例如：

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> array;
    array.push_back(10);   // array.size() = 1
    int num = 4;
    cout << array.size() - num << "\n";
    return 0;
}
```

在 64 位环境中，`size_t` 常见为 64 位无符号整数。运行可能输出：

`18446744073709551613`

这个数怎么来的？

* `array.size()` 是 `1`（无符号 64 位）
* `num` 是 `4`（有符号 int），参与运算时会被转成无符号 64 位
* 无符号下的 `1 - 4` 会发生回绕：等价于 `2^64 + 1 - 4 = 2^64 - 3`
* `2^64 - 3 = 18446744073709551613`

你也可以用补码理解：

* `[1]`（64 位）：`...0001`
* `[-4]`（补码）：`...1100`
* `1 + (-4)` 得到：`...1101`
* 把这个比特模式按无符号解释，就是 `2^64 - 3`

## 修改代码

避免用无符号结果去做“可能为负”的减法判断。一个简单的写法是把条件换成加法形式：

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> array;
    array.push_back(10);   // array.size() = 1
    int num = 4;

    // 不用无符号数做减法，避免回绕
    for (int i = 1; i + num < (int)array.size(); ++i) {
        cout << "hello world" << "\n";
    }
    return 0;
}
```

{% links %}

- site: "cppreference：size_t"
  owner: "cppreference"
  url: "[https://en.cppreference.com/w/cpp/types/size_t](https://en.cppreference.com/w/cpp/types/size_t)"
  desc: "size_t 的定义与用途"
  image: "assets/pexels-11.jpg"
  color: "#e9546b"
- site: "cppreference：usual arithmetic conversions"
  owner: "cppreference"
  url: "[https://en.cppreference.com/w/cpp/language/usual_arithmetic_conversions](https://en.cppreference.com/w/cpp/language/usual_arithmetic_conversions)"
  desc: "混合整型运算时的提升与转换规则"
  image: "assets/pexels-11.jpg"
  color: "#4e7cff"
  {% endlinks %}

