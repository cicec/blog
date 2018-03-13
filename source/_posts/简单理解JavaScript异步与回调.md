---
title: 简单理解JavaScript异步与回调
date: 2018-03-13 18:30:48
tags: 前端相关
---

## 前戏

- 很简单，花一点时间稍微理解一下就好了。

## 正文

### 异步？

通常来说，浏览器在执行JavaScript代码时，会按照代码顺序逐行执行。也就是说，上一行代码没有执行完毕，是不会继续向下执行的，而是会一直等待到这行代码返回了执行结果才会继续执行。

这里先创建一个person对象用于理解：

``` js
const person = {
    eat: () => { console.log('吃饭...') },
    sleep: () => { console.log('睡觉...') },
    getUp: () => { console.log('起床啦！') }
}
```

接着分别调用其内部定义的方法：

``` js
person.eat() // 吃饭...
person.sleep() // 睡觉...
person.getUp() // 起床啦！
```

这里person对象每次调用内部的方法时，都需要等待上一个调用的方法执行结束后才会执行，就好像一个人一般来说是不能边吃饭边睡觉的，必须要等吃完了饭才能去睡觉。那么这种代码的执行方式就被称为同步。

很显然，异步就是与同步相对的执行方式，也就是调用代码后**不等待调用结果**，而是立即返回，再在将来通过某种手段来获取调用结果。

接着上面的例子，做些修改：

``` js
const person = {
    eat: () => { console.log('吃饭...') },
    sleep: () => { console.log('睡觉...') },
    getUp: () => { console.log('起床啦！') },
    setAlarmClock: function setAlarmClock(time) {
        setTimeout(() => {
            this.getUp()
        }, time)
        console.log(`我定了一个闹钟，${time}ms后叫我起床。`)
    }
}

person.setAlarmClock(1000) // 我定了一个闹钟，1000ms后叫我起床。
person.eat() // 吃饭...
person.sleep() // 睡觉...
// （1s后）起床啦！
```

这里person对象首先执行了setAlarmClock函数，也就是定了个闹钟，而它定完闹钟之后并不会坐在闹钟面前等着它响，而是先去吃饭、睡觉，等1s钟后闹钟响了再起床。

这种执行方式就叫做异步。

一句话概括，就是**同步等结果，异步不等结果**。

就这么简单。

### 回调？

那么什么是回调呢？

上文提到过，异步代码调用后不会等待调用结果，而是将来通过某种手段获取结果。这里的“某种手段”也就是回调。

**回调一般出现在异步编程中，用于得到异步处理的结果。回调函数可以作为参数传递到其他函数中，让其在合适的时候调用**。

再接着上文的例子。假如闹钟响了之后还需要获取当前的睡眠状态，来判断要不要接着睡：

``` js
const person = {
    eat: () => { console.log('吃饭...') },
    sleep: () => { console.log('睡觉...') },
    getUp: () => { console.log('起床啦！') },
    setAlarmClock: function setAlarmClock(time, callback) {
        setTimeout(() => {
            this.getUp()
            const state = '困成狗'
            callback(state)
        }, time)
        console.log(`我定了一个闹钟，${time}ms后叫我起床。`)
    }
}

person.setAlarmClock(1000, (state) => {
    if (state === '困成狗') {
        console.log('再睡一会...')
    } else {
        console.log('赶紧起...')
    }
}) // 我定了一个闹钟，1000ms后叫我起床。
person.eat() // 吃饭...
person.sleep() // 睡觉...
// （1s后）起床啦！
//  再睡一会...
```

这里执行setAlarmClock函数时会传递一个参数callback也就是回调函数，在闹钟响了之后就会执行这个函数，并将当前的状态作为参数传递到这个函数中。这样就能在回调函数中得到这段异步代码的结果并根据结果做出不同的操作了。

## 结语

- 写结语了吗？
- 没有哦。