---
layout: post
title:  命令行的速记工具cheat
categories: [OS]
description: cheat
keywords: linux, cheat
---

命令行的速记工具 cheat, 原文作者: 张志强 , 2021-06-01 , 共 1145 字 cheat 提供命令行的速记记录，在命令行下，随时增加笔记随时查看，用的时间越长越强大！[原文链接地址](https://zhiqiang.org/it/cheat-cheatsheets.html)

### 安装 cheat 和 cheatsheets
首先安装 cheat （注意是通过 pip 而不是 apt ），如果没有权限可以添加 --user 安装到自己的目录：

sudo -E pip install cheat 
注意cheat只是一个提供编辑和查看速记的工具，这时候直接运行会提示No cheatsheet：

$ cheat rsync

No cheatsheet found for tar
我们还需要手工安装一些别人已经编辑好的速记素材，比如官方的https://github.com/cheat/cheatsheets。这份素材已经位于安装目录，但默认没有启用。下面命令可以启用这份官方素材：

sudo ln -s /usr/local/lib/python3.8/dist-packages/usr/share/cheat/ /usr/share/cheat

我同步了一份官方的，同时增加了一些自己的素材：https://gitee.com/zhiqiang.org/cheatsheets，安装方法如下：

cd ~/.cheat;

git clone https://gitee.com/zhiqiang.org/cheatsheets .
用户可以在~/.cheat关联自己的库，以保存和同步自己的记录。注意，这种方法安装的素材只能本人使用。公用素材需要安装在/usr/share/cheat。


2、用法
查看速记：

    ```
    $ cheat grep
    "常规用法，显示所有匹配的行，-r 表示 recursive 递归搜索。支持正则表达式
    grep 'xxx' <file_or_path> -r
    " 显示所有不匹配的行，-v
    grep -v 'xxx' src -r
    " 只显示匹配部分，-o only
    grep -o 'xxx.*.jpg' src -r
    " 不显示文件名
    grep -h 'xxx' src -r
    " 显示行号（默认不显示行号，有点违反直觉），-n number
    grep -n 'xxx' src -r
    " 忽略大小写 -i，ignore
    grep -i 'xxx' src -r
    " 显示匹配行的上下文，-A2 上面2行，-B3 下面3行，-C4 上下各4行。
    grep -A2 -B3 -C4 'xxx' src
    ```


编辑速记：

cheat -e grep
如果关联了自己的速记库，记得编辑之后同步推送到库，以免丢失。

---