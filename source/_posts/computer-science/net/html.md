---
title: HTML 标签与 CSS 基础
date: 2023/05/30
categories:
- [计算机科学, 计算机网络]
tags: 前端
cover: assets/pexels-09.jpg
---

# HTML 标签

+++info 阅读提示
这篇笔记是 HTML 常用标签速查（属性、table、form、iframe）+ CSS 基础（语法、引入方式、选择器、优先级、字体与背景、伪类/伪元素）。内容偏“清单式”，适合边写页面边查。
+++

## 属性

通用属性（除 `br` 标签外基本都适用）：
- `id`：给标签取唯一名称
- `class`：类名，给标签取一个类名
- `style`：设置标签行内样式
- `title`：鼠标移到标签上显示提示内容

自定义属性：通常用于传值或图片懒加载（优化网页）：
- 格式：`data-*`
- 示例：`<img data-src="图片名" alt="提示文字">`
- 示例：`<p data-id="goodsid">` 用来传值

## table 标签

用于呈现格式化数据，灵活性较差，由行列组成。

基本格式：
```html
<table>
  <tr>
    <th>表头</th>
    <td>内容</td>
  </tr>
</table>
````

Emmet 示例：

* `table+tr*2>td{内容$}*3`

常用属性（对表格进行调整）：

* `border`：边框宽度（默认单位像素）
* `width`：宽度
* `cellspacing`：单元格边框间距
* `cellpadding`：单元格文本与边框距离
* `align`：表格对齐方式 `center/right/left`（默认）

表格跨行/跨列：

* `rowspan`：跨行
* `colspan`：跨列

完整表格结构：

* `caption`：标题
* `thead`：表头
* `tbody`：表体
* `tfoot`：表尾（总结）

Emmet 示例：

* `table[border=1 align=center]>caption{student}+(thead>tr>th*4)+(tbody>tr*3>td*4)+(tfoot>tr>td[colspan=4])`

## form 表单

> 实现前后端交互的重要标签。

常用属性：

* `name`：表单名称，用于区分表单
* `action`：表单数据提交地址（后端文件如 `.jsp/.py/.php/.aspx` 或 URL），`#` 表示提交到当前页面
* `method`：提交方式 `GET/POST`

### 表单元素

#### input：输入类

通过 `type` 区分输入形式：

* `text`：单行文本框
  常见属性：`minlength/maxlength`、`readonly`、`value`、`disabled`、`pattern`（正则）
* `password`：密码（属性与 `text` 类似）
* `submit`：提交按钮（提交表单到后台）
* `image`：图片提交按钮（`src` 替代 `value`，其余与 `submit` 类似）
* `radio`：单选按钮
  常见属性：`checked`、`disabled`、**`name`（同 name 互斥，保证单选）**
* `checkbox`：复选框（属性与 `radio` 类似）
* `button`：普通按钮（常用于脚本；`disabled`、`value`）
* `reset`：重置按钮（清空输入，还原初始状态）
* `file`：文件上传按钮

#### textarea：文本域

多行文本框，可输入回车；（传统上文本域内容写在标签体内，现代也可配合脚本获取/设置）。

常用属性：

* `name`
* `id`
* `cols`：列数
* `rows`：行数
* `placeholder`
* `minlength/maxlength`
* `required`（必须输入）

#### button：按钮

普通按钮：可单独使用（不必写在 `form` 中）；若放在 `form` 中且未指定 `type`，可能表现为提交按钮（建议显式写 `type="button"`）。

#### select：下拉列表

* 用 `option` 呈现选项
* `multiple`：可多选（此时更像列表框）
* `size`：调整显示行数

## iframe

> 框架集：将多个文件组合到一个页面中，尽量少用；会破坏前进/后退体验，也不利于 SEO。

常用属性：

* `name`
* `src`：引入外部 HTML
* `scrolling`：滚动条 `yes/no/auto`
* `width`
* `height`
* `frameborder`：边框 `1` 有 / `0` 无
* `marginheight`：上下边距
* `marginwidth`：左右边距

---

# CSS 层叠样式表

+++info 阅读提示
HTML 表达内容结构，CSS 表达样式美化网页，做到“结构（HTML）与表现（CSS）分离”。
+++

## 语法

```css
选择器 {
  属性: 属性值;
}
```

## 引用方式

* 行内样式：直接在标签上写 `style=""`
* 内部样式：在文件内部写 `style` 标签

```html
<style>
  /* 样式内容 */
</style>
```

* 外部样式：创建 `.css` 文件，用 `link` 引入

```html
<link rel="stylesheet" href="style.css">
```

* 导入外部样式：在 `style` 里用 `@import`

```html
<style>
  @import "test.css";
</style>
```

区别：

* 行内样式只作用于当前标签
* 内部样式作用于当前 HTML 文件
* 外部样式可被多个 HTML 复用（项目中最常用）

`link` vs `@import`：

* `link` 除 CSS 外还能定义其他事务；`@import` 只能加载 CSS
* `link` 一般随页面加载并行获取；`@import` 往往在解析到时再加载（可能更晚）
* `link` 兼容性更好；`@import` 在低版本浏览器支持较差
* `link` 可被 JavaScript 更方便地控制与替换；`@import` 不便

## 选择器分类

1. 通配选择器：`*` 匹配所有元素（慎用，性能差）

2. 标签选择器：匹配标签名

```css
span { display: block; }
```

3. 类选择器：匹配 `class`

```css
.lab { color: aqua; }
```

4. id 选择器：匹配 `id`

```css
#name { color: pink; }
```

5. 后代/上下文选择器：根据层级选择

```css
.lab li { color: chartreuse; }
```

6. 伪类选择器：表示元素的特殊状态

* `a:visited` 已访问
* `a:link` 未访问
* `a:hover` 悬停
* `a:active` 激活
* `:focus` 获得焦点
* `:first-child` / `:last-child`

## 选择器分组

多个选择器使用同一份样式，常用于公共样式（例如：`h1, h2, h3 { ... }`）。

## 选择器继承

子元素可以继承父元素的部分样式（如字体相关），但并非全部属性都继承。

## 优先级

通常情况下（从低到高）：

* 外部样式 < 内部样式 < 内联样式

权重（常见经验）：

* `!important` 最高
* 内联样式
* `id` 选择器
* 类/伪类/属性选择器
* 标签选择器
* 通配选择器

## 字体

常用属性：

* `font-size`：`px` / `%`（基于父元素）
* `font-family`：字体名，包含空格要加引号
* `font-style`：`normal/italic/oblique`
* `font-weight`：`normal/bold/bolder/lighter/100~900`
* `line-height`：`normal/20px/1.5`
* `color`
* `text-decoration`：修饰（如下划线）
* `text-align`：对齐方式
* `text-transform`：大小写（`none/capitalize/uppercase/lowercase`）
* `text-indent`：首行缩进（`20px/2em`）

## CSS 背景

* `background-color`
* `background-image`
* `background-repeat`
* `background-position`
* `background-attachment`：`scroll`（随页面滚动）/ `fixed`（背景不动）
* `background`：复合属性

## 属性选择器

* `[attr]`：包含指定属性名
* `[attr=value]`：属性值等于指定值
* `[attr~=value]`：属性值包含指定词（空格分隔）
* `[attr^=value]`：属性值以指定值开头
* `[attr$=value]`：属性值以指定值结尾

## 关系选择器

* 空格：后代选择器（A B）
* `>`：子元素选择器（A > B）
* `+`：相邻兄弟选择器（A + B）

## CSS 伪元素

> 伪类用于“已有元素处于某种状态”；伪元素用于“创建文档树中不存在的部分并添加样式”。

* `:before` / `:after` / `:first-letter` / `:first-line`（可用单冒号或双冒号）
* `::selection` / `::placeholder` / `::backdrop`（通常使用双冒号）

---

# CSS 浮动与盒子

+++info 阅读提示
浮动（float）用于让块级元素不独占一行，但会脱离标准文档流并引发“父元素塌陷”等问题；盒子模型是布局与排版的基础。
+++

> 让块级标签不独占一行，使多个块级元素可以排在同一行。

## 浮动

浮动会让元素脱离文档流，不再占用标准流位置。

### float 属性值

* `left`
* `right`
* `none`：默认值（不浮动），可用于取消浮动

### 浮动问题

浮动后，后续元素可能不会自动“换行到下一行”，布局容易出现重叠/塌陷等问题。

### 清除浮动

目的：让后面的元素正常排布、让父元素正确包裹浮动子元素。

常用方法（一般第 2 种更常见）：

1. 添加空标签并设置 `clear: both;`

* `clear: left` 清除左浮动
* `clear: right` 清除右浮动
* `clear: both` 清除左右浮动
* `clear: none` 不清除

2. 给父元素添加：`overflow: hidden;`

* 超出隐藏的同时可触发 BFC，从而清除浮动影响

3. 使用伪元素 `::after` 清除浮动

```css
.parent::after {
  content: "";
  display: block;
  clear: both;
}
```

## CSS 盒子模型

每个元素都可以看作一个盒子，由以下部分组成：

* `margin` 外边距
* `border` 边框
* `padding` 内边距
* `content` 内容

### display：设置元素显示方式

常用值：

* `none`：不显示
* `block`：块级显示（可把行内元素变成块）
* `inline`：行内显示（可把块元素变成行内）
* `inline-block`：行内块（既可同一行排布，又能设置宽高）

{% links %}

- site: "MDN：HTML"
  owner: "MDN"
  url: "[https://developer.mozilla.org/zh-CN/docs/Web/HTML](https://developer.mozilla.org/zh-CN/docs/Web/HTML)"
  desc: "HTML 标签、属性与语义化参考"
  image: "assets/pexels-04.jpg"
  color: "#e9546b"
- site: "MDN：CSS"
  owner: "MDN"
  url: "[https://developer.mozilla.org/zh-CN/docs/Web/CSS](https://developer.mozilla.org/zh-CN/docs/Web/CSS)"
  desc: "CSS 语法、选择器、布局与参考手册"
  image: "assets/pexels-04.jpg"
  color: "#4e7cff"
  {% endlinks %}

