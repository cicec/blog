---
title: 实现基于React的全局提示组件Toast
date: 2018-08-03 10:23:56
tags: 前端相关
---

## 前戏

- API 风格参考 Ant Design 的 [Message 全局提示](https://ant.design/components/message-cn/)

## 正文

### 需求分析

- Toast 不需要同页面一起被渲染，而是根据需要被随时调用。
- Toast 是一个轻量级的提示组件，它的提示不会打断用户操作，并且会在提示的一段时间后自动关闭。
- Toast 需要提供几种不同的消息类型以适应不同的使用场景。
- Toast 的方法必须足够简洁，以避免不必要的代码冗余。

#### 最终效果： 

- [点此预览](https://clancysong.github.io/react-components/dist/toast/)

#### 调用示例

``` jsx
Toast.info('普通提示')
Toast.success('成功提示', 3000)
Toast.warning('警告提示', 1000)
Toast.error('错误提示', 2000, () => {
    Toast.info('哈哈')
})
const hideLoading = Toast.loading('加载中...', 0, () => {
    Toast.success('加载完成')
})
setTimeout(hideLoading, 2000)
```

### 组件实现

Toast 组件可以被分为三个部分，分别为：
- notice.js：Notice。无状态组件，只负责根据父组件传递的参数渲染为对应提示信息的组件，也就是用户最终看到的提示框。
- notification.js：Notification。Notice 组件的容器，用于保存页面中存在的 Notice 组件，并提供 Notice 组件的添加和移除方法。
- toast.js：控制最终对外暴露的接口，根据外界传递的信息调用 Notification 组件的添加方法以向页面中添加提示信息组件。

项目目录结构如下：

```
├── toast
│   ├── icons.js
│   ├── index.js
│   ├── notice.js
│   ├── notification.js
│   ├── toast.css
│   ├── toast.js
```

为了便于理解，这里从外部的 toast 部分开始实现。

#### toast.js

因为页面中没有 Toast 组件相关的元素，为了在页面中插入提示信息，即 Notice 组件，需要首先将 Notice 组件的容器 Notification 组件插入到页面中。这里定义一个 createNotification 函数，用于在页面中渲染 Notification 组件，并保留 addNotice 与 destroy 函数。

``` js
function createNotification() {
    const div = document.createElement('div')
    document.body.appendChild(div)
    const notification = ReactDOM.render(<Notification />, div)
    return {
        addNotice(notice) {
            return notification.addNotice(notice)
        },
        destroy() {
            ReactDOM.unmountComponentAtNode(div)
            document.body.removeChild(div)
        }
    }
}
```

接着定义一个全局的 notification 变量用于保存 createNotification 返回的对象。并定义对外暴露的函数，这些函数被调用时就会将参数传递回 Notification 组件。因为一个页面中只需要存在一个 Notification 组件，所以每次调用函数时只需要判断当前 notification 对象是否存在即可，无需重复创建。

``` js
let notification
const notice = (type, content, duration = 2000, onClose) => {
    if (!notification) notification = createNotification()
    return notification.addNotice({ type, content, duration, onClose })
}

export default {
    info(content, duration, onClose) {
        return notice('info', content, duration, onClose)
    },
    success(content, duration, onClose) {
        return notice('success', content, duration, onClose)
    },
    warning(content, duration, onClose) {
        return notice('warning', content, duration, onClose)
    },
    error(content, duration, onClose) {
        return notice('error', content, duration, onClose)
    },
    loading(content, duration = 0, onClose) {
        return notice('loading', content, duration, onClose)
    }
}
```

#### notification.js

这样外部工作就已经完成，接着需要完成 Notification 组件内部的实现。首先 Notification 组件的 state 属性中有一个 notices 属性，用于保存当前页面中存在的 Notice 的信息。并且 Notification 组件拥有 addNotice 和 removeNotice 两个方法，用于向 notices 中添加和移除 Notice 的信息（下文简写为 notice）。

添加 notice 时，需要使用 getNoticeKey 方法为这个 notice 添加唯一的key值，再将其添加到 notices 中。并根据参数提供的 duration，设置定时器以在到达时间后将其自动关闭，这里规定若 duration 的值小于等于0则消息不会自动关闭，而是一直显示。最后方法返回移除自身 notice 的方法给调用者，以便其根据需要立即关闭这条提示。

调用 removeNotice 方法时，会根据传递的key的值遍历 notices，如果找到结果，就触发其回调函数并从 notices 中移除。

最后就是遍历 notices 数组并将 notice 属性传递给 Notice 组件以完成渲染，这里使用 react-transition-group 实现组件的进场与出场动画。

（注：关于页面中同时存在多条提示时的显示问题，本文中采用的方案时直接将后一条提示替换掉前一条消息，所以代码中添加 notice 直接写成了 notices[0] = notice 而非 notices.push(notice)， 如果想要页面中多条提示共存的效果可以自行修改。）

``` js
class Notification extends Component {
    constructor() {
        super()
        this.transitionTime = 300
        this.state = { notices: [] }
        this.removeNotice = this.removeNotice.bind(this)
    }

    getNoticeKey() {
        const { notices } = this.state
        return `notice-${new Date().getTime()}-${notices.length}`
    }

    addNotice(notice) {
        const { notices } = this.state
        notice.key = this.getNoticeKey()
        if (notices.every(item => item.key !== notice.key)) {
            notices[0] = notice
            this.setState({ notices })
            if (notice.duration > 0) {
                setTimeout(() => {
                    this.removeNotice(notice.key)
                }, notice.duration)
            }
        }
        return () => { this.removeNotice(notice.key) }
    }

    removeNotice(key) {
        this.setState(previousState => ({
            notices: previousState.notices.filter((notice) => {
                if (notice.key === key) {
                    if (notice.onClose) notice.onClose()
                    return false
                }
                return true
            })
        }))
    }

    render() {
        const { notices } = this.state
        return (
            <TransitionGroup className="toast-notification">
                {
                    notices.map(notice => (
                        <CSSTransition
                            key={notice.key}
                            classNames="toast-notice-wrapper notice"
                            timeout={this.transitionTime}
                        >
                            <Notice {...notice} />
                        </CSSTransition>
                    ))
                }
            </TransitionGroup>
        )
    }
}
```

#### notice.js

最后剩下的 Notice 组件就很简单了，只需要根据 Notification 组件传递的信息输出最终的内容即可。可以自行发挥设计样式。

``` js
class Notice extends Component {
    render() {
        const icons = {
            info: 'icon-info-circle-fill',
            success: 'icon-check-circle-fill',
            warning: 'icon-warning-circle-fill',
            error: 'icon-close-circle-fill',
            loading: 'icon-loading'
        }
        const { type, content } = this.props
        return (
            <div className={`toast-notice ${type}`}>
                <svg className="icon" aria-hidden="true">
                    <use xlinkHref={`#${icons[type]}`} />
                </svg>
                <span>{content}</span>
            </div>
        )
    }
}
```

#### 18-08-05 更新

- 调整页面中多条提示的显示方案为：允许页面中同时存在多条提示；
- 修复添加提示时返回的移除提示方法实际不生效的问题；
- 优化组件样式与过渡效果。

注：主要改动为 notification.js 文件中的 addNotice 和 removeNotice 方法。原文中的代码未作修改，修改后的代码请参见 [项目源码](https://github.com/clancysong/react-components/tree/master/components/toast)。

## 结语

- [项目源码](https://github.com/clancysong/react-components/tree/master/components/toast)
- [项目文档](https://github.com/clancysong/react-components/blob/master/docs/toast.md)