---
layout: post
title: Jekyll 教程
categories: [GitHub, web]
description: 使用Jekyll搭建个人静态网站
keywords: jekyll, GitHub,web
---

## 简介

Jekyll 的核心是一个文本转换引擎。这个系统的大概是：你给它的内容可以用你最喜欢的标记语言编写，可以是 Markdown，是 Textile 或者是纯粹的 HTML，然后 Jekyll 通过一个或一系列布局文件混合它们。在整个过程中，你可以调整网站 URL 的样式，布局中显示的数据等等。这一切都是通过编辑文本文件完成的，而静态网站就是它的最终产品

## 目录结构

基本的 Jekyll 站点通常看起来像这样：

```{}
.
├── _config.yml
├── _data
|   └── members.yml
├── _drafts
|   ├── begin-with-the-crazy-ideas.md
|   └── on-simplicity-in-technology.md
├── _includes
|   ├── footer.html
|   └── header.html
├── _layouts
|   ├── default.html
|   └── post.html
├── _posts
|   ├── 2007-10-29-why-every-programmer-should-play-nethack.md
|   └── 2009-04-26-barcamp-boston-4-roundup.md
├── _sass
|   ├── _base.scss
|   └── _layout.scss
├── _site
├── .jekyll-metadata
└── index.html # can also be an 'index.md' with valid YAML Frontmatter

```

## gitalk 添加第三方评论插件

参考链接: https://www.cnblogs.com/Tom-shushu/p/10970526.html

- 1.首先需要到 GitHub 上去新建一个仓库用于存放评论的内容
- 2.在设置中打开 isue 功能
- 3.需要注册一个 Github Application 具体步骤如下
  (1)申请地址：https://github.com/settings/applications/new
  注意两个 URL 就是你网站的域名。名字随便写，描述随便写。

  (2)完成之后便到了如下页面：
  其中 Client ID 和 Client Secret 是我们需要的东西

- 4.如上步骤完成之后，接下来你就会体验到 Gitralk 的方便之处：
  只需要将如下代码引入你想添加评论的 html 或者 jsp 页面中就可以使用了

## Reference

- [Jekyll 使用教程笔记 一：简介、快速开始、基本用法、目录结构](https://juejin.im/post/5b235a1cf265da597568a97d)
- [Jekyll 使用教程笔记 三：Front Matter、写文章](https://juejin.im/post/5b3497ffe51d4558c5394a35)
- [Jekyll 使用教程笔记四: 创建页面、静态文件、变量](https://juejin.im/post/5b35b5e8e51d4558b64f3ab9)
- [Jekyll 使用教程笔记五：合集、数据文件](https://juejin.im/post/5b35ee5de51d455cd054c4d1)

## 优秀博客推荐

[纯洁的微笑 blog](http://www.ityouknow.com/)
[博客搭建](http://www.ityouknow.com/other/2018/09/16/create-blog.html)
