---
layout: post
title: 安装电脑主机
categories: [OS]
description: 安装电脑主机的注意事项
keywords: 主机, 电脑
---

## 主机安装

- 端口有电，但鼠标，显示器没信号不显示，内存条可能没插好，先插一条试试，插入第二和第四个插槽效果更好，先插第四条
- 排针顺序
  - |Power Led+| Power Led -| Power Switch+ |Power Switch -|
    | HDD led+ |HDD led-|-|-|
- BIOS 启动设置
  - 找不到启动盘
    - LaunchCSM [Disabled]->[Enabled]
    - OS Type[Other OS]-> [Windows UEFI mode]
  - 两个显示屏设置默认的为 graphics

## 显示屏BIOS 设置

  首选显卡 [Auto]->[CPU Graphics]