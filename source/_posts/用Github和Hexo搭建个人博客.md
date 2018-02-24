---
title: 用Github和Hexo搭建个人博客
date: 2018-02-01 20:24:00
tags: 前端相关
---

## 前戏

嗯。。

## 创建Github Page仓库

首先需要在github中创建一个名为**你的名字.github.io**的新仓库来存放页面代码。这样在这个仓库中部署的代码可以通过**你的名字.github.io**这个网址来直接访问预览页面，而且以后还可以用自己购买的域名来替换github分配的网址。

## 搭建本地环境

Hexo是在本地环境中将markdown文件和主题一起渲染打包，然后发布推送到github上，所以首先需要部署本地环境。

本机安装了node的前提下，打开一个安全的路径，直接逐条执行以下命令。

``` shell
npm install -g hexo-cli
hexo init myBlog
cd myBlog
npm i
```

然后打开myBlog目录中的_config.yml，稍微做一点配置。

1. 第6行title，改为网站的名字。
2. 第9行author，改为作者的名字。
3. 最后一行type改为 `type: git` 。
4. 最后一行添加 `repo: {你的仓库地址}` 。

配置完成后再执行最后一句 `npm install hexo-deployer-git --save` 。

这样本地环境就搭建好了，很简单，想要看看效果的话执行`hexo deploy`稍等片刻打开github page地址就可以看到搭建好的博客了。

## 发布文章

博客当然是要用来写文章的，而发布文章的流程也同样很简单。

1. 执行 `hexo new 文章名称` 。
2. 进入source/_posts目录，打开刚刚新建的文章，开始创作。
3. 写完文章后执行 `hexo generate` 生成修改后的博客页面。
4. 再执行 `hexo deploy` 就可以发布到github了。

## 修改主题

主题当然也是可以换的。

1. 先从[这里](https://github.com/hexojs/hexo/wiki/Themes)挑一个自己看着顺眼的主题，复制它的clone地址。
2. 执行 `git clone git@github.com:iissnan/hexo-theme-next.git themes/{主题名字}` 下载主题。
3. 修改_config.yml文件75行为刚刚下载的主题名字。
4. 执行 `hexo generate` 和 `hexo deploy` 打包发布。

这样就完成了。

![](http://p4nfph69y.bkt.clouddn.com/kgd0s.png)



## 结语

以前没有用过Hexo，尝试了一下发现确实很方便，但是还是觉得少了点什么，毕竟个人主页还是喜欢自己一点一点搭建出来的感觉吧。
