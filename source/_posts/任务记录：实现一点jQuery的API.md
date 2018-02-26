---
title: 任务记录：实现一点jQuery的API
date: 2018-02-26 19:00:40
tags: 前端相关
---

## 任务要求：

1. 选择器功能：传入选择器或DOM对象，返回包装后的jQuery对象（伪数组）。
2. .addClass()方法：用于为节点添加类。
3. .setText()方法：用于设置节点内的文本。

## 实现过程

### 选择器

jQuery本身就是一个函数，它需要将传入的选择器或DOM对象包装成jQuery对象并返回。所以首先需要先声明一个变量 $nodes 来存放包装后的对象：

``` js
const $nodes = { length: 0 }
```

然后判断传入参数的类型：

``` js
if (typeof node === 'string') {
    ...  // 如果参数是字符串，那么就是选择器
} else if (typeof node === 'object') {
    ...  // 如果参数是对象，那么就是DOM节点
} else {
    ...  // 都不是就报错
}
```

判断完毕后就根据参数的类型做相应的操作。这里为了保持输出结果的一致性，无论选择器选中一个还是多个节点，都统一使用伪数组来存储。

``` js
if (typeof node === 'string') {
    selectedNodes = document.querySelectorAll(node)
    for (let i = 0; i < selectedNodes.length; i++) {
        $nodes[i] = selectedNodes[i]
        $nodes.length += 1
    }
} else if (typeof node === 'object') {
    $nodes[0] = node
    $nodes.length = 1
} else {
    throw new Error('Param error')
}
```

最后返回这个$nodes变量就可以了。

``` js
return $nodes
```

### .addClass() 和 .setText()

接下来需要为这个对象添加两个方法让它看起来有点用处。首先是.addClass()方法，这里直接在被返回的$nodes对象上面添加：

``` js
$nodes.addClass = (className) => {
    for (let i = 0; i < $nodes.length; i++) {
        $nodes[i].classList.add(className)
    }
}
```

这里为了保证$nodes中所有的节点都能被添加class，需要对$nodes遍历一下，再分别为每个节点添加class。.setText()也是同理：

``` js
$nodes.setText = (text) => {
    for (let i = 0; i < $nodes.length; i++) {
        $nodes[i].textContent = text
    }
}
```

## 用$代替jQuery

至此这个简单的jQuery的功能就已经完成了。最后还需要将jQuery函数绑定到全局变量以方便调用。然而jQuery这个名字还是稍微长了点，这时可以再将jQuery再赋值到全局的$变量上，这样看起来就方便多了。

``` js
window.jQuery = jQuery
window.$ = jQuery
```

OK，完成。