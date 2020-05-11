---
layout: wiki
title: Scientific web
categories: [Tools]
description: 科学上网
keywords: website
---

[科学上网镜像导航](https://ac.scmor.com/)

### adblock plus

    * block zhihu 添加元素

    只屏蔽首页Timeline，不影响搜索和内容页面。

    步骤：

    打开：ABP设置 - 高级 - 我的过滤列表

    输入zhihu.com##.Topstory-mainColumnCard ， 添加，大功告成。



    ps. 同时推荐添加 zhihu.com##.AdblockBanner， 关闭顶部abp提示框。

### chrome 参数设置

```{}
修改注册表以下地方:
HKEY_CLASSES_ROOT\http\shell\open\command
HKEY_CLASSES_ROOT\https\shell\open\command
HKEY_CLASSES_ROOT\ChromeHTML\shell\open\command
HKEY_CLASSES_ROOT\ftp\shell\open\command
在"chrome 路径" -- "%1"中间插入 chrome 的参数:
"chrome 路径" --user-data-dir="Z:\User Data" -- "%1"
```
