---
title: 浅谈：CSS-Grid布局
date: 2018-01-25 19:02:00
tags: 前端相关
---

![](http://upload-images.jianshu.io/upload_images/8017344-719e5b5ad978fb29.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 前戏

没有前戏。。
。。。
真的没有。

## Grid布局简介

不多bb先丢个文档上来（嗯） [网格布局 - CSS | MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Grid_Layout)。

## Grid布局示例

### 拼方块

要使用网格布局，与flexbox一样，需要先定义一个容器。

> display: grid | inline-grid | subgrid

很简单，定义好容器之后就可以在容器内分割网格了，与table有些类似。

``` html
<div class="container">
    <div class="item">1</div>
    <div class="item">2</div>
    <div class="item">3</div>
    <div class="item">4</div>
    <div class="item">5</div>
</div>
```

``` css
.container {
    border: solid 1px rgb(142, 150, 255);
    display: grid;
    grid-template-columns: repeat(3, 160px);
    grid-template-rows: repeat(3, 160px);
}
```

![grid-template-row(column).png](http://upload-images.jianshu.io/upload_images/8017344-8ef8e48d2a020606.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这里先创建了1个container和5个放在容器中的item，并且为了便于观察，为container添加了一个边框。而container的gird-template-columns和grid-template-rows就是为容器划分网格的属性。

> grid-template-columns: <track-size> ... | <line-name> <track-size> ...
   grid-template-rows: <track-size> ... | <line-name> <track-size> ...

这两个属性一个负责划分列，一个负责划分行。格式例如：**grid-template-rows: 50px 100px 150px;**，需要多少行（列）就写多少个值，每个值都代表对应那一行（列）的宽度。

上述代码将容器划分为了3*3、每个单元格的宽高均为160px的网格。

但是这些方块被连成了一片，为了区分他们的边界，这里需要给这些元素添加一点间距。

``` css
.container {
    border: solid 2px rgb(142, 150, 255);
    display: grid;
    grid-template-columns: repeat(3, 160px);
    grid-template-rows: repeat(3, 160px);
    grid-gap: 8px;
}
```

![grid-gap.png](http://upload-images.jianshu.io/upload_images/8017344-3464694e7e5319fe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

grid-gap属性负责控制网格线的宽度，可以直接理解为行（列）间距。可以分别控制行间距和列间距。

> grid-column-gap: <line-size>
> grid-row-gap: <line-size>
> grid-gap: <grid-row-gap> <grid-column-gap>

现在容器内一共有9个单元格，但只有5个方块，为了让这些方块能够填满这个容器，只需要为每个方块指定分别占用哪些格子即可。

``` css
.item:nth-child(1) {
    grid-column: 1 / 3;
}

.item:nth-child(2) {
    grid-column: 3 / 4;
    grid-row: 1 / 3;
}

.item:nth-child(3) {
    grid-column: 2 / 4;
    grid-row: 3 / 4;
}

.item:nth-child(4) {
    grid-row: 2 / 4;
}
```

![grid-row(column).png](http://upload-images.jianshu.io/upload_images/8017344-2f4d16accb8e79f9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

grid-column和grid-row属性分别指定网格项在列和行上占用的网格区域。例如 **grid-column: 2 / 4;** 指定该网格项在列方向上占用第2条网格线到第4条网格线之间的区域，注意这两个数值指定的是**网格线**而不是单元格（除此之外还可以通过第二个值指定该网格项跨越的单元格数量，默认为1）。

> grid-column: <start-line> / <end-line> | <start-line> / span <value>
   grid-row: <start-line> / <end-line> | <start-line> / span <value>

同样可以为这两个属性分别设置起点与终点

> grid-column-start: <number> | <name> | span <number> | span <name> | auto
   grid-column-end: <number> | <name> | span <number> | span <name> | auto
   grid-row-start: <number> | <name> | span <number> | span <name> | auto
   grid-row-end: <number> | <name> | span <number> | span <name> | auto

而在划分网格时，可以为网格线定义名称，并使用grid-row(column)属性直接通过名称选择网格线而非通过数字（默认）。例如：

``` css
.container {
    grid-template-columns: [line-1] 160px [line2] 160px [line3] 160 [line4] ;
}

.item:nth-child(1) {
    grid-column: line1 / line3;
}
```

### 实现圣杯布局

到了这里，就可以马上动手用grid实现一个简单的圣杯布局。

![圣杯布局1.png](http://upload-images.jianshu.io/upload_images/8017344-b32992672810cd61.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

很简单，也没什么好说的，主要是熟悉属性用法。

### 再拚一遍方块

除了使用grid-row和grid-column属性为网格项指定占用的网格区域，还可以通过grid-area属性来控制。为了使用这一属性，需要先在容器内定义属性grid-template-areas。

``` css
.container {
    display: grid;
    grid-template-columns: repeat(3, 160px);
    grid-template-rows: repeat(3, 160px);
    grid-gap: 8px;
    grid-template-areas: 
        "item1 item1 item2"
        "item4 item5 item2"
        "item4 item3 item3";
}

.item:nth-child(1) {
    grid-area: item1;
}

.item:nth-child(2) {
    grid-area: item2;
}

.item:nth-child(3) {
    grid-area: item3;
}

.item:nth-child(4) {
    grid-area: item4;
}
```

可以看出grid-template-areas属性定义了可视化的网格结构，为每个单元格设置名称并通过设置网格项的grid-area属性来指定其所占用的网格区域。

得到的结果是一样的，因为grid-template-areas使用可视化语法书写，所以很好理解（可以使用任意数量的相邻的.来声明单个空单元格。 只要这些点号之间没有空格，他们就代表了一个单一的单元格）。

而grid-area除了可以设置网格项对应的名称外，还可以作为grid-row和grid-column的缩写。

> grid-area: <name> | <row-start> / <column-start> / <row-end> / <column-end>

### 其他属性

#### 网格容器属性

**justify-items和align-items**：两个属性一个是定义**单元格内的元素相对单元格**行轴方向的对齐方式，一个是定义列轴方向的对齐方式。与flexbox的属性很相似，不过要注意的是flex是没有justify-items这个属性的。

> justify-items: start | end | center | stretch

> align-items: start | end | center | stretch

**justify-content和align-content**：同样和flexbox的属性相似。一个是定义**单元格在网格容器内**行轴方向的对齐方式，一个是定义列轴方向的对齐方式。

> justify-content: start | end | center | stretch | space-around | space-between | space-evenly

> align-content: start | end | center | stretch | space-around | space-between | space-evenly

**grid-auto-columns和grid-auto-rows**：指定网格的隐式轨道的大小。也就是当网格项的显式定位超出了网格容器的范围，在缺少轨道的位置填充的大小。

``` css
.container {
    display: grid;
    grid-template-columns: repeat(3, 160px);
    grid-template-rows: repeat(3, 160px);
    grid-gap: 8px;
    grid-template-areas: 
        "item1 item1 item2"
        "item4 item5 item2"
        "item4 item3 item3";
    grid-auto-columns: 160px;
}

.item:nth-child(5) {
    grid-column: 5 / 6;
}
```

![grid-auto-columns.png](http://upload-images.jianshu.io/upload_images/8017344-f3a8841e761069d0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这里第5个方块被显式定位到第5和第6条网格线之间，明显超出了容器的范围，于是在第4和第6条网格线之间便存在了2条隐式轨道，而grid-auto-columns就是用来定义这条轨道的宽度。若没有定义该属性，则轨道宽度为0（默认）。

> grid-auto-columns: <track-size>
   grid-auto-rows: <track-size>

**grid-auto-flow**：用来定义未被定位的网格项的放置规则。很好理解，在最开始的实例中，网格中的5个方块就是横着一行一行排下来的（默认row），如果将这个属性改为column，则会纵向一列一列排过来。

> grid-auto-flow: row | column | row dense | column dense

**grid**：grid-template-rows，grid-template-columns，grid-template-areas，grid-auto-rows，grid-auto-columns和grid-auto-flow的简写。

> grid: none | <grid-template-rows> / <grid-template-columns> | <grid-auto-flow> [<grid-auto-rows> [/ <grid-auto-columns>]]

#### 网格项属性

**justify-self和align-self**：看名字就知道干什么用的了吧。控制单个网格项在单元格内的对齐方式。

> justify-self: start | end | center | stretch

> align-self: start | end | center | stretch

## 结语

关于grid布局各属性的详细信息如果仍有疑问请手动查阅[MDN文档](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Flexible_Box_Layout)。
