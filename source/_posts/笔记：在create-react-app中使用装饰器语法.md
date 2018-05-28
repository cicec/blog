---
title: 笔记：在create-react-app中使用装饰器语法
date: 2018-05-28 19:36:39
tags: 前端相关
---

在使用mobx时可以使用装饰器语法来简化代码以及便于阅读。然而装饰器语法是不被babel默认支持的，如果直接对这种语法进行转义的话就会报语法错误。这时就需要手动启用装饰器。

首先在项目中安装依赖：

> npm i --save-dev babel-plugin-transform-decorators-legacy

接着在.babelrc中启用：

``` json
{
    "presets": ["es2015", "stage-1"],
    "plugins": ["transform-decorators-legacy"]
}
```

这样装饰器就被启用了，代码也能够被正确地转义。

但很多时候我们使用create-react-app来创建项目，而create-react-app中的配置文件是不能直接修改的，为了修改babel的配置，可以使用 `npm run eject` 命令来弹出配置，这样就能够在package.json文件中找到babel的配置选项：

``` json
...
"babel": {
    "presets": [
        "react-app"
    ]
},
...
```

然后像上面一样正常添加配置就好了：

``` json
"babel": {
    "presets": [
        "react-app"
    ],
    "plugins": [
        "transform-decorators-legacy"
    ]
},
```

当然如果不想这样大动干戈地弹出全部配置，也可以直接在node_mudules目录中手动找到对应的配置文件单独进行修改。

这里直接进入 `node_modules/babel-preset-react-app/index.js` 目录中，找到plugins选项，直接添加装饰器支持就好了：

``` js
const plugins = [
    ...
+   require.resolve('babel-plugin-transform-decorators-legacy'),
    require.resolve('babel-plugin-transform-es2015-destructuring'),
    // class { handleClick = () => { } }
    require.resolve('babel-plugin-transform-class-properties'),
    ...
]
```

#### Tips：

在VSCode中直接使用装饰器语法会被提示错误，虽然并没有什么影响，但是看着也还是会不爽。那么只需要在设置中覆盖如下配置（推荐在工作区设置），错误就会消失了。

``` json
{
    "javascript.implicitProjectConfig.experimentalDecorators": true
}
```




