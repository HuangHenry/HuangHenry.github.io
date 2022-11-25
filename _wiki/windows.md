---
layout: wiki
title: Windows
cate1: Tools
cate2:
categories: [Tools]
description: Windows 使用技巧
keywords: Windows
---

- windows 自动锁屏 <https://windows10.pro/win10-auto-lock-screen/>

方法一：传统的“屏幕保护程序设置”实现自动锁屏
在 Win10 任务栏的 Cortana 搜索框中输入“屏幕保护程序”，点击搜索结果中的“更改屏幕保护程序（控制面板）”即可打开“屏幕保护程序设置”窗口。如图：

其实我们并不是要设置屏幕保护程序，而且现在普遍使用的液晶显示器并不需要设置屏幕保护程序，所以屏幕保护程序可以设置为“无”。关键是下面的设置项：

勾选“在恢复时显示登录屏幕”，然后把“等待”时间设置成你希望电脑闲置多长时间自动锁屏的时间，一般几分钟即可。设置时间过短的话，你平时使用时，稍微没有操作的时间长一点儿就会自动锁屏，也不方便。

最后点击“确定”关闭设置窗口。以后你再离开电脑时，电脑闲置超过指定的时间后就会自动锁屏了。

只有输入登录密码才能查看和使用电脑。如图：

- 该链接中的方法三：另外一种方法
  本地策略-安全选项 交互式登录：计算机不活动限制

https://www.jb51.net/os/win10/663055.html

## windows 自动更新

服务，windows update 停止 禁用 恢复下面 选无操作

## VPN 和本地网络不能同时连接的问题

在 VPN 连接上点击鼠标右键，选择“属性”

在“VPN 连接属性”窗口选择“网络”标签，选择“IPv4”，再选择“属性”，关于 windows 10 各个版本 VPN 协议 属性无法打开 需要关闭远程网关的一个办法
<http://www.jeepxie.net/article/852394.html>

[VPN 连上无法上网的问题](https://diorscoder.github.io/2016/10/16/VPN%E8%BF%9E%E6%8E%A5%E4%B9%8B%E5%90%8E%E6%97%A0%E6%B3%95%E4%B8%8A%E7%BD%91%E9%97%AE%E9%A2%98%E8%A7%A3%E5%86%B3%E6%96%B9%E6%B3%95%EF%BC%81/)

## 远程桌面 crash

[使用远程桌面协议后 Windows 10 DWM.EXE 100%的解决办法](https://www.landiannews.com/archives/60962.html)

## 新装电脑配置

- listary
- anaconda3
- pycharm
- teamviewer

## 更改 cmd 外观设置

首先我们更改一下外观，由于 WSL 的会话窗口配色实际上取决于 CMD 属性里颜色标签里面的配色，默认的实在是太丑了，既没有高亮字符也不清晰，严重影响开发效率。

这就需要用到一个开源的 CMD 配色工具：[ColorTool](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fmicrosoft%2Fterminal%2Freleases%2Ftag%2F1708.14008)

下载解压后，用 CMD 打开到解压路径，然后运行下列的命令，CMD 的界面就会改变了，选一个自己觉得好看的。

colortool.exe -b deuteranopia
colortool.exe -b OneHalfDark
colortool.exe -b OneHalfLight
colortool.exe -b solarized_dark
colortool.exe -b solarized_light
然后重新打开 Ubuntu 窗口，就会发现配色也被修改了。如果不满意，还可以打开终端的设置属性界面，微调下字体和光标颜色等。这里建议将 ”将 Ctr+Shift+ C/V 用作复制/粘贴的快捷键” 给勾上，光标形状设置为实心框，字体设置为 Consolas。

作者：希希里之海
链接：https://www.jianshu.com/p/3e627ff45ccb
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

## 软链接

```{cmd}
    mklink /j  d:/linked_short_name_folder  d:/src_original_folder
```


https://www.windstock.com.cn/


## cherry 机械键盘
* Fn键切换，长按Fn键3-5秒