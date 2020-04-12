---
layout: wiki
title: Tmux 使用梳理
categories: [linux, tmux]
description: tmux
keywords: keyword1, keyword2
---

## Tmux 快捷键

```{}
1）进入 tmux 面板后，一定要先按 ctrl+b，然后松开，再按其他的组合键才生效。

2）常用到的几个组合键：
ctrl+b ? 显示快捷键帮助
ctrl+b 空格键 采用下一个内置布局，这个很有意思，在多屏时，用这个就会将多有屏幕竖着展示
ctrl+b ! 把当前窗口拆分成为一个独立的新窗口
ctrl+b " 模向分隔窗口
ctrl+b % 纵向分隔窗口
ctrl+b q 显示分隔窗口的编号
ctrl+b o 跳到下一个分隔窗口。多屏之间的切换
ctrl+b 上下键 上一个及下一个分隔窗口
ctrl+b C-方向键 调整分隔窗口大小
ctrl+b & 确认后退出当前 tmux
ctrl+b [ 复制模式，即将当前屏幕移到上一个的位置上，其他所有窗口都向前移动一个。
ctrl+b c 创建新窗口
ctrl+b n 选择下一个窗口
ctrl+b l 最后使用的窗口
ctrl+b p 选择前一个窗口
ctrl+b w 以菜单方式显示及选择窗口
ctrl+b s 以菜单方式显示和选择会话。这个常用到，可以选择进入哪个 tmux
ctrl+b t 显示时钟。然后按 enter 键后就会恢复到 shell 终端状态
ctrl+b d 脱离当前会话；这样可以暂时返回 Shell 界面，输入 tmux attach 能够重新进入之前的会话
```

## 参考

[Tmux 使用教程](https://www.ruanyifeng.com/blog/2019/10/tmux.html)-阮一峰
