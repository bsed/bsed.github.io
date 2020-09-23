---
title: Flexbox学习指南
date: 2016-08-27 20:48:00
updated: 2016-08-28 20:56:53
tags: 
- compressor
categories: 
- linux

---
## 基础概念
Flexbox 不同于其他单一的 CSS 属性，它有专属它自己的一些属性和抽象概念。主要可分为两大类:

* Flex container (容器)
* Flex item (子项目)

想要将一个 HTML 元素设定为 Flex container 相当容易，你只需要在元素中加入 `display: flex` 或 `display: inline-flex` 。两者的差别类似 `display: block` 和 `display: inline-block` 的差别。所有在 Flex container 内的子元素就是 Flex item。如下图:

![flexbox.png][1]

在图中可观察到的另一个重要观念就是 Flexbox 是以两个主轴为基础，main axis 与 cross axis。这边先大致说明一下图中的各个关键字:

* **main axis:** 这是 Flexbox 内的主轴之一，所有的 Flex item 都会沿著 main axis 排列。要特别注意的是，main axis 的方向不一定是水平的，它会依照 `flex-direction` 的值做改变
* **main start | main end:** main axis 的起点边界与终点边界
* **main size:** Flexbox 的宽或高，方向依照 `flex-direction` 的值决定
* **cross axis:** Flexbox 内的另一个主轴，垂直于 main axis。方向依照`flex-direction` 的值做改变
* **cross start | cross end:** cross axis 的起点边界与终点边界
* **cross size:** Flexbox 的宽或高，方向依照 `flex-direction` 的值决定


<!--more-->


在 Flexbox 裡，所有元素的位置都是相对于上面所述的两个主轴。随著方向改变，所有的 flex item 位置也会有所变化。

## Flex Container 属性介绍
### flex-direction

![flex-direction1.svg][2]
此属性决定了 main axis 的方向，也代表著所有 flex item 的内容会如何排列。

```css
.container {
    flex-direction: row | row-reverse | column | column-reverse;
}
```

* **row:** (预设值) 当 `writing-mode` 为 `ltr` 时，main axis 方向为从左到右。当 `writing-mode` 为 `rtl` 时，main axis 方向为从右到左
* **row-reverse:** 当 `writing-mode` 为 `ltr` 时，main axis 方向为从右到左。当 `writing-mode` 为 `rtl` 时，main axis 方向为从左到右
* **column:** 同 row ，方向改为从上到下
* **column-reverse:** 同 row-reverse ，方向改为从下到上

### flex-wrap

![flex-wrap.svg][3]

Flexbox 预设会将所有 Flex item 都排列在同一排，此属性可让 flex container 在同一排塞不下所有 item 时产生换行效果。

```css
.container{
    flex-wrap: nowrap | wrap | wrap-reverse;
}
```

* **no-wrap:** (预设值) 不换行，当 `writing-mode` 为 `ltr` 时，排列方向为从左到右。当 `writing-mode` 为 `rtl` 时，排列方向为从右到左
* **wrap:** 换行，方向同上
* **wrap-reverse:** 换行，方向与 wrap 相反

### flex-flow

`flex-flow` 属性是 `flex-direction` 和 `flex-wrap` 的缩写，可快速定义 flex container 的主轴。预设为 `row nowrap` 。写法如下：

```css
.container{
    flex-flow: <flex-direction> || <flex-wrap>
}
```

### justify-content

![justify-content.svg][4]

`justify-content` 是用来定义 flex item 在 main axis 上的排列位置。它会依照赋予的值分配多馀的空白空间。

```css
.container {
    justify-content: flex-start | flex-end | center | space-between | space-around;
}
```

* **flex-start:** (预设值) Flex item 会靠往 `main-start` 对齐
* **flex-end:** Flex item 会靠往 `main-end` 对齐
* **center:** 于 main axis 上置中 Flex item 
* **space-between:** 将空白平均分配到所有 Flex item 之间，第一个 item 靠在 `main-start`，最后一个 item 靠在 `main-end`
* **space-around:** 平均分配空白到所有的 Flex item 周围，包括第一个与最后一个 item。你可能会注意到最左和最右边的间距会比中间小，原因是在于中间的间隔是左边 item 的右间隔加上右边 item 的左间隔

### align-items

![align-items.svg][5]

此属性是用来定义 flex item 在 cross axis 上的排列位置。你可以这样想：cross axis 与 `align-items` 的关系就像 main axis 与 `justify-content`之间的关系。

```css
.container {
    align-items: flex-start | flex-end | center | baseline | stretch;
}
```

* **flex-start:** Flex item 会靠往 `cross-start` 对齐
* **flex-end:** Flex item 会靠往 `cross-end` 对齐
* **center:** 于 cross axis 上置中 Flex item
* **baseline:** Flex item 会依照各个 item 的基准线对齐
* **stretch:** (预设值) 自动伸缩将多馀的空间补满

### align-content

![align-content.svg][6]

`align-content` 主要是用在当 main-axis 有换行时。此属性相当于 `justify-content` 与 `align-items` 的综合版，不过它排列的是整个内容，而不是单一行。排列对应的轴是 cross axis 而非 main-axis。注意：当 main axis 没换行时，此属性不会有作用。

```css
.container {
    align-content: flex-start | flex-end | center | space-between | space-around | stretch;
}
```

* **flex-start:** 所有内容会靠往 `cross-start` 对齐
* **flex-end:** 所有内容会靠往 `cross-end` 对齐
* **center:** 于 cross axis 上置中所有内容
* **space-between:** 将空白平均分配到每行内容之间，第一行靠在 `cross-start`，最后一行靠在 `cross-end`
* **space-around:** 平均分配空白到每行内容周围，包括第一行与最后一行
* **stretch:** (预设值) 自动伸缩将多馀的空间补满

## Flex Item 属性介绍
所有包在 Flex Container 内的直接子元素都是 Flex item，包含文字。`float`，`clear` 和 `vertical-align`对 Flex item 没有作用。Flex item 内的元素与内容不会被 Flexbox 影响。举个例子：在 Flex item 元素上加入 `float: left` 不会有任何反应，但在 Flex item 的子元素裡加入 `float: left` 则会正常往左浮动。

### order

![order-2.svg][7]

`order` 是一个非常好用的属性，它赋予了我们不用修改 HTML 就能更动元素位置的能力。一般来说，每个 item 都是依照在 HTML 中的排列顺序出现，但此属性可控制元素出现的先后顺序。值越小的排越前面。

```css
.item {
    order: <integer>;
}
```

### flex-grow

![flex-grow.svg][8]

此属性赋予每个 item 在需要时自动佔满剩馀宽度的能力。接受的值为一个无单位的数字，此数字决定了 item 在剩馀的空间内该佔满多少比例的宽度。如果所有的 item 都将 `flex-grow` 设为 1，那麽每个 item 都会有相同宽度并佔满所有空间。如果将其中一个的 `flex-grow` 改为 2 的话，被修改的元素将会佔满比其他元素两倍大的空间。

```css
.item {
    flex-grow: <number>; /* default 0 */
}
```

### flex-shrink

此属性为 `flex-grow` 的相反。

```css
.item {
    flex-shrink: <number>; /* default 1 */
}
```

### flex-basis

此属性会依照 `flex-direction` 定义 item 最小的宽度或高度，有点类似 `min-width`。这个属性可以翻译成：当需要伸缩时，依照 `flex-grow` 或 `Flex-shrink` 的值来伸缩，但是宽度或高度不能小于 X px 或 em。

```css
.item {
    flex-basis: <length> | auto; /* default auto */
}
```

### flex

`flex` 属性是 `flex-grow`，`flex-shrink` 和 `flex-basis` 的缩写，第二和第三个参数 (`flex-shrink` 和 `flex-basis`) 可不给。预设值为 `0 1 auto`。

```css
.item {
    flex-basis: <length> | auto; /* default auto */
}
```

建议使用此属性而非独立使用上述三个属性来设定 item 宽/高度。

### align-self

![align-self.svg][9]

此属性可以将个别 item 的 `align-items` 属性覆盖过去进而提供更为弹性的设计模式。

```css
.item {
    align-self: auto | flex-start | flex-end | center | baseline | stretch;
}
```

对于此属性的值的解说，请参考 `align-items` 部分。

## 总结

Flexbox 是一个非常强大的排版模式，它轻易的解决了以往许多需要使用各种怪异的方法来解决的问题。但，它的整体架构与常见的 Grid 系统又不太一样，开发者需要换一个角度来思考版面的配置。希望这篇文章有让大家准备好在未来的专案裡使用 Flexbox。如果文章内有任何问题，也欢迎各位讨论纠正。


  [1]: https://imgs.gnux.cn/usr/uploads/2016/08/2199399516.png
  [2]: https://imgs.gnux.cn/usr/uploads/2016/08/458847658.svg
  [3]: https://imgs.gnux.cn/usr/uploads/2016/08/910166628.svg
  [4]: https://imgs.gnux.cn/usr/uploads/2016/08/832432726.svg
  [5]: https://imgs.gnux.cn/usr/uploads/2016/08/3335045285.svg
  [6]: https://imgs.gnux.cn/usr/uploads/2016/08/1566240730.svg
  [7]: https://imgs.gnux.cn/usr/uploads/2016/08/3477435474.svg
  [8]: https://imgs.gnux.cn/usr/uploads/2016/08/4255710098.svg
  [9]: https://imgs.gnux.cn/usr/uploads/2016/08/4076424757.svg