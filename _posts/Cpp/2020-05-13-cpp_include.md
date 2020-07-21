---
layout: post
title: cpp运行添加库文件
categories: Basis
description: cpp,include
keywords: cpp,include,c++
---

## gcc 和 g++头文件和库路径的寻找和添加

对所有用户有效修改/etc/profile 文件

对个人有效则修改~/.bashrc 文件

#在 PATH 中找到可执行文件程序的路径。

```
export PATH =$PATH:$HOME/bin (可一次指定多个搜索路径，":"用于分隔它们)
```

#gcc 找到头文件的路径

```
C_INCLUDE_PATH=/usr/include/libxml2:/MyLib
export C_INCLUDE_PATH
```

#g++找到头文件的路径

```
CPLUS_INCLUDE_PATH=\$CPLUS_INCLUDE_PATH:/usr/include/libxml2:/MyLib
export CPLUS_INCLUDE_PATH
```

#找到动态链接库的路径

```
LD_LIBRARY_PATH=\$LD_LIBRARY_PATH:/MyLib
export LD_LIBRARY_PATH
```

#找到静态库的路径

```
LIBRARY_PATH=\$LIBRARY_PATH:/MyLib
export LIBRARY_PATH
```

## 参考链接

<https://www.cnblogs.com/zxouxuewei/p/7263568.html>
