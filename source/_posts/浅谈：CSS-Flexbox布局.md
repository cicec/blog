---
title: 浅谈：CSS-Flexbox布局
date: 2018-01-24 19:33:33
tags: 前端相关
---

![cover.jpg](http://upload-images.jianshu.io/upload_images/8017344-36cf42b752298e3d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 前戏

在正式开始接触弹性盒子布局之前，我们先稍微写一点代码初步感受一下。

``` html
<div class="container">
    <div class="box"></div>
</div>
```

``` css
.container {
    min-height: 100vh;
    display: flex;
}

.box {
    width: 150px;
    height: 150px;
    background: #ffaaaa;
    margin: auto;
}
```

![flexbox居中显示.png](http://upload-images.jianshu.io/upload_images/8017344-dc278f3cd54115ac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在这里，我们创建了一个容器和其内部的方块，并将容器display属性设置为flex。可以看到，对容器内的方块，我们只是添加了 **margin: auto;** 这一行代码，便实现了其相对容器水平与垂直上的完全居中。也就是说，flexbox首先便解决了css一直以来在垂直居中上的痛点。

## Flexbox简介

先丢一张MDN上面关于弹性盒布局的介绍图

![](http://upload-images.jianshu.io/upload_images/8017344-6b31409ba46fba6c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

没看懂的话没有关系，这里是[MDN的官方文档](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Flexible_Box_Layout/Using_CSS_flexible_boxes)，认真看。
以上

---

。。。
啊不是，没看懂的话接着往下看。

## Flexbox示例

要使用弹性盒布局，首先便要定义一个弹性容器（flex container）。弹性容器可以通过设置display的值为flex或inline-flex定义，其中flex会被外界视为块级元素，而inline-flex则会被视为行内元素。处在弹性容器内部的元素，就被视为弹性项目（flex item）.

#### flex container

在上面的例子中，container元素已经被添加了 **display: flex;** 属性，也就是说，container元素已经作为一个弹性容器存在，而box元素就是一个弹性项目。
基于上面的例子，我们做一些修改。

- 将box的margin属性删掉，这里暂时用不到它了。
- 将原本的1个box元素复制为6个.

刷新页面，我们会发现6个box元素挤在了一起，这并不弹性。

在这里，只需要为弹性容器添加一条属性

``` css
.container {
    padding: 20px;
    display: flex;
    justify-content: space-around;
}
```

刷新页面，6个box便已经均匀地分布在这一行上了。

![justify-content.png](http://upload-images.jianshu.io/upload_images/8017344-1bc273ab03899b49.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在介绍justify-content属性之前，我们先了解一下主轴的概念：

主轴就是弹性项目在弹性容器的排布方向，默认是横向从左到右的，也就是说，在向弹性容器中塞元素的时候，默认是从左到右一个一个放好的。 当然，主轴的方向是可变的，可以是从右向左，甚至可以是从上到下的。主轴的方向由属性flex-direction控制。

> flex-direction: row | row-reverse | column | column-reverse

而justify-content属性控制的便是弹性项目在主轴上的对齐方式，之前6个box挤在了一起，是因为该属性默认是向主轴的起点对齐。而后我们将justify-content设置为space-around，作用就是使主轴上的弹性项目均匀分布。

> justify-content: flex-start | flex-end | center | space-between | space-around;

接下来我们向容器内再加一些方块。将原来的6个box再复制一遍，现在容器内有12个方块了。刷新页面，我们发现这些方块又连成了一片，甚至还超出了容器的边界，这并不弹性。

于是我们需要为弹性容器再添加一条属性

``` css
.container {
    padding: 20px;
    display: flex;
    justify-content: space-around;
    flex-wrap: wrap;
}

.box {
    width: 150px;
    height: 150px;
    background: #ffaaaa;
    margin: 10px 0;
}
```

为了便于观察，我们为box添加一点外边距。再次刷新，可以看到多出来的方块排列到了下一行，而且当我们调整浏览器窗口大小时，方块们会自动排列整齐。

![flex-wrap.png](http://upload-images.jianshu.io/upload_images/8017344-bcb1124c8bf08f45.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在这里我们需要了解关于侧轴的概念：

侧轴是定义弹性容器内是否以多行显示，以及多行的排布方向。侧轴的方向与主轴是垂直的，如果主轴是横向的，那么侧轴就是纵向的，反之亦然。侧轴由属性flex-wrap控制，默认为单行显示，也就是不换行。（主轴与侧轴的属性可以简写为flex-flow。例如 **flex-flow: row wrap;**）。

> flex-wrap: nowrap | wrap | wrap-reverse

侧轴与主轴一样可以定义行在侧轴线的对齐方式，由属性align-content控制。

> align-content: flex-start | flex-end | center | space-between | space-around | stretch;

接着我们修改几个方块的高度，让它们看起来不那么一样。

``` css
.box:nth-child(1) {
    height: 200px;
}

.box:nth-child(2) {
    height: 350px;
}

.box:nth-child(1) {
    height: 250px;
}

.box:nth-child(4) {
    height: 300px;
}
```

可以看到一行的高度是由这一行内最高的元素决定的，那么怎样让元素相对这一行的高度垂直居中呢。

``` css
.container {
    padding: 20px;
    display: flex;
    justify-content: space-around;
    flex-wrap: wrap;
    align-items: center;
}
```

![align-items.png](http://upload-images.jianshu.io/upload_images/8017344-c10dd05eb5328464.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这样，就控制了每一行的元素都以该行的中间位置对齐。而align-items属性控制的便是行内项目的对齐方式。

> align-items: flex-start | flex-end | center | baseline | stretch

#### flex items

以上介绍的都是为弹性容器定义的属性，而我们同样可以为每个弹性容器分别定义不同的属性。

order：控制弹性项目在弹性容器中布局的顺序。例如我们将原本的最后一个方块的order值设置为-1，因为其他方块的order均为默认值0，则最后一个方块将会排在第一位。

> order: <integer> 

align-self：为单独的伸缩项目定义在行内的对齐方式（对应align-items）

> align-self: auto | flex-start | flex-end | center | baseline | stretch;

最后的三个属性都与弹性项目本身的缩放有关，放在一起来说。

我们将上面的示例再稍微改动一下。首先将方块们都删掉，留下四个就可以。然后为了便于观察，为这四个方块设置不同的背景颜色。最后分别为他们添加flex-grow属性，并设置不同的值，例如：

``` css
.box:nth-child(1) {
    background: #ffaaaa;
    flex-grow: 2;
}

.box:nth-child(2) {
    background: #f2ffaa;
    flex-grow: 1;
}

.box:nth-child(3) {
    background: #aaffc4;
    flex-grow: 4;
}

.box:nth-child(4) {
    background: #c8aaff;
    flex-grow: 3;
}
```

刷新页面，我们发现四个方块之间没有了间距，并且似乎都宽了不少。

![flex-grow.png](http://upload-images.jianshu.io/upload_images/8017344-7e3280de4ca03981.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

不难看出，flex-grow属性定义的便是弹性项目的扩展能力。但需要注意的是，flex-grow与其他方块的值是成比例的。例如上述示例中，四个方块的属性值分别为2、1、4、3，总和为10，则方块1扩展能力就是2/10，它能占用的剩余空间就是1/5的大小。而如果只有一个方块定义了flex-grow属性，因为其他方块值均为默认值0（不扩展），那么无论这个方块的flex-grow属性为多少，它都将得到全部的剩余空间。

> flex-grow: <number>; /* default 0 */

与之相对应的，就是flex-shrink属性。该属性定义弹性项目的收缩能力。在容器缩小时随着容器等比例缩小。

> flex-shrink: <number>; /* default 1 */

flex-basis：这个属性定义弹性项目在主轴上所占的长度。很容易理解，与宽度类似，但与宽度不同的是，flex-basis是在**主轴**方向上的长度，也就是说，如果主轴是纵向的，那么它定义的就是高度（可以这样理解）。

flex-grow, flex-shrink, flex-basis可以简写为flex属性，例如 **flex: 2 2 20%;** 

## 结语

关于flexbox布局各属性的详细信息如果仍有疑问请手动查阅[MDN文档](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Flexible_Box_Layout)
