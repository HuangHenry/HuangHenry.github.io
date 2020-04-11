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

## Reference

- [Jekyll 使用教程笔记 一：简介、快速开始、基本用法、目录结构](https://juejin.im/post/5b235a1cf265da597568a97d)
- [Jekyll 使用教程笔记 三：Front Matter、写文章](https://juejin.im/post/5b3497ffe51d4558c5394a35)
- [Jekyll 使用教程笔记四: 创建页面、静态文件、变量](https://juejin.im/post/5b35b5e8e51d4558b64f3ab9)
- [Jekyll 使用教程笔记五：合集、数据文件](https://juejin.im/post/5b35ee5de51d455cd054c4d1)
