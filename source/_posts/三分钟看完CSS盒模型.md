---
title: 三分钟看完CSS盒模型
date: 2018-02-06 19:53:00
tags: 前端相关
---

## 前戏

很简单的概念，没有理解难点。惯例开场丢图

![CSS盒模型.png](http://upload-images.jianshu.io/upload_images/8017344-4236ecc81ca52a15.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 什么是盒模型

在html中，每个元素都是一个盒子。因为网页一般来说都是二维系统，所以可以想象在页面上观察到的就是这些盒子的俯视图。

上文图中就是一个完整的盒模型。其中的content也就是最中间的那一块就是盒子中的内容；外面border那一圈代表的就是盒壁的厚度，也就是边框；而边框到内容之间的距离，就被称作内边距；最后外面那一圈，被称作外边距，它可以让盒子们不会全部挤到一起，而是保持适当的间距。

也就是说，一个完整的盒模型占据的宽度实际是content宽度+左右padding+左右border+左右margin的总长度，而高度则是content高度+上下padding+上下border+上下margin的总长度。

## box-sizing

那么在css中，width和height指的是谁的宽度和高度呢。

``` css
.box {
  width: 200px;
  height: 200px;
}
```
实际上，直接在css中设置宽高的话设置的是content的宽高。。是的，既不是整个盒模型的宽高，也不是盒子的宽高，而是盒子中的内容的宽高，也就是说上述代码只是指定了content的大小为200px*200px，而如果再给这个元素添加padding和border属性，盒子就会随着变大。

``` css 
.box {
  padding: 20px;
}
```

这里为box元素添加了一个20px的内边距，而content的宽高已经设定好不会再变，那么盒子就向上下左右各扩展20px的距离充当内边距。如果添加边框则是同理。而如果置了背景、颜色或者图片，这些样式将会延伸到padding上。

这就很令人费解了，大部分人第一反应都应该会觉得指定元素的宽高就应该是盒子的宽高，尤其在元素拥有背景或者边框时，就更容易产生错觉。而且当需要调整一个元素的宽度和高度使还需要时刻注意元素的内边距和边框，让人十分困扰。

好在现在这个“错误”已经有了解决方案了，现在只需要给元素添加一个属性

``` css
.box {
  box-sizing: border-box;
}
```

这样元素的宽高就包含了元素的内边距和边框。如果再给这个元素指定宽度高度以及内边距，那么这个元素的实际内容区的宽度就是width-padding-border。这样就可以更容易地去调整一个元素的宽高。实际开发中可以根据自己的习惯选择不同的盒模型规范。

## 外边距合并

外边距合并就是当两个**垂直边距**相遇时，形成的外边距不是两个外边距的总和，而是两个外边距中的较大值。例如元素1外边距是30px，元素2外边距是20px，元素2处在元素1下方，那么他们之间的间距就是30px。

三种情况会产生外边距合并，稍微记一下就好了。

1. 相邻叠加的两个块级元素，如果它们的上下边距相遇，即会产生叠加。
2. 块级元素自身没有内容、内边距、边框时，它的上下外边距就会相遇产生叠加。
3. 块级父元素和其第一个/最后一个子元素之间没有内边距、边框时，它们的边距就会相遇产生叠加。

## 结语

惯例放MDN文档。
- [盒子模型](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Box_Model/Introduction_to_the_CSS_box_model)
- [box-sizing](https://developer.mozilla.org/zh-CN/docs/Web/CSS/box-sizing)
- [外边距合并](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Box_Model/Mastering_margin_collapsing)
