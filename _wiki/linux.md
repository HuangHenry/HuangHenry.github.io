---
layout: wiki
title: Linux/Unix/WSL
categories: Linux
description: 类 Unix 系统下的一些常用命令和用法。
keywords: Linux,WSL
mathjax: true
---

类 Unix 系统下的一些常用命令和用法。

## linux

- zsh
  - https://zhuanlan.zhihu.com/p/19556676
    -oh my zsh
  - https://www.jianshu.com/p/d194d29e488c
-

## 实用命令

### 软件安装更新

- update 更新资源

  - 只检查 不更新（已安装的可用更新 给出汇总报告）

  ```{}
    sudo apt-get update
  ```

- 对系统软件都进行更新升级

  - 更新已安装的软件包

  ```{}
    sudo apt-get upgrade
  ```

- 安装软件包

  ```{}
    sudo apt-get install
  ```

- 删除软件包

  ```{}
    sudo apt-get remove package
  ```

### grep

- grep or 操作符

  - 使用 \|

    ```{bash}
      grep 'pattern1\|pattern2' filename
    ```

  - 使用选项 -E

    ```{bash}
    grep -E 'pattern1|pattern2' filename
    ```

- grep and 操作

  - 使用 -E 'pattern1.\*pattern2'

    ```{bash}
      grep -E 'pattern1.*pattern2' filename
      grep -E 'pattern1.*pattern2|pattern2.\*pattern1' filename
    ```

  - 使用多个 grep 命令

    ```{bash}
      grep -E 'pattern1' filename | grep -E 'pattern2'
    ```

- grep not 操作

  - 使用选项 grep -v

  ```{bash}
    grep -v 'pattern1' filename
  ```

### fuser

查看文件被谁占用。

```sh
fuser -u .linux.md.swp
```

### id

查看当前用户、组 id。

### lsof

查看打开的文件列表。

> An open file may be a regular file, a directory, a block special file, a character special file, an executing text reference, a library, a stream or a network file (Internet socket, NFS file or UNIX domain socket.) A specific file or all the files in a file system may be selected by path.

#### 查看网络相关的文件占用

```sh
lsof -i
```

#### 查看端口占用

```sh
lsof -i tcp:5037
```

#### 查看某个文件被谁占用

```sh
lsof .linux.md.swp
```

#### 查看某个用户占用的文件信息

```sh
lsof -u mazhuang
```

`-u` 后面可以跟 uid 或 login name。

#### 查看某个程序占用的文件信息

```sh
lsof -c Vim
```

注意程序名区分大小写。

## 使用 shell 命令行

- [fzf][1] 模糊匹配
  https://www.tecmint.com/fzf-fuzzy-file-search-from-linux-terminal/
- tmux
- [z.lua](https://www.jianshu.com/p/a56766f2b80e)
- tldr
		npm install -g tldr

## WSL 开通 ssh 端口

```{bash}
  #重装openssh并启动
  apt-get remove openssh-server
  apt-get update
  apt-get install openssh-server
  sudo i /etc/ssh/sshd_config
    修改 PasswordAuthentication no 为 PasswordAuthentication yes
    :wq 保存退出
  service ssh --full-restart
  ### ssh 开机自动启动
  sudo systemctl enable ssh
```


## ssh 连上时提示异常

```{}
=> There were exceptions while processing one or more plugins. See
     /var/log/landscape/sysinfo.log for more information.
```

[^_^]: # (<font color='red'>\var\log\landscape\sysinfolog</font>里的异常日志:)

<span style="color:red;">\var\log\landscape\sysinfo.log</span>里的异常日志:

[^-^]: 注释部分,别人看不到$\color{red}{\var\log\landscape\sysinfo.log}$里的异常日志:

```{}
ERROR    Network plugin raised an exception.
Traceback (most recent call last):
  File "/usr/lib/python3/dist-packages/landscape/sysinfo/sysinfo.py", line 99, in run
    result = plugin.run()
  File "/usr/lib/python3/dist-packages/landscape/sysinfo/network.py", line 32, in run
    for info in self._get_device_info():
  File "/usr/lib/python3/dist-packages/landscape/lib/network.py", line 181, in get_active_device_info
    speed, duplex = get_network_interface_speed(sock, interface)
  File "/usr/lib/python3/dist-packages/landscape/lib/network.py", line 261, in get_network_interface_speed
    raise e
  File "/usr/lib/python3/dist-packages/landscape/lib/network.py", line 253, in get_network_interface_speed
    fcntl.ioctl(sock, SIOCETHTOOL, packed)  # Status ioctl() call
OSError: [Errno 22] Invalid argument
```

解决方法，修改（创建）配置文件（/etc/landscape/client.conf），禁用 landscape-sysinfo 的 Network plugin：

```{}
[sysinfo]
exclude_sysinfo_plugins = Temperature, Network
```

<http://manpages.ubuntu.com/manpages/cosmic/man1/landscape-sysinfo.1.html>

## Reference

- [wsl 安装和使用笔记](https://low.bi/p/Pd19w2jwRO0)

- [玩转 Linux 系统](https://github.com/HuangHenry/Python-100-Days/blob/master/Day31-35/31-35.%E7%8E%A9%E8%BD%ACLinux%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F.md)

[1]: https://www.tecmint.com/fzf-fuzzy-file-search-from-linux-terminal/

## ubuntu 新装配置

[unbuntu配置](https://zhuanlan.zhihu.com/p/56253982)

## ubuntu python3 配置

/bin/sh: python: not found

原因
python 可能被 python2 或者 python 3 代替了导致映射不到。

解决方法
命令行执行：

ls -l /usr/bin/python\*
然后会出现：

```
lrwxrwxrwx 1 root root 9 3 月 16 09:09 /usr/bin/python3 -> python3.6
-rwxr-xr-x 1 root root 4526456 11 月 7 18:44 /usr/bin/python3.6
-rwxr-xr-x 1 root root 4526456 11 月 7 18:44 /usr/bin/python3.6m
lrwxrwxrwx 1 root root 10 3 月 16 09:09 /usr/bin/python3m -> python3.6m
```

我们发现确实没有 /usr/bin/python 映射。这里我选择重建映射， 我们将/usr/bin/python 映射到 /usr/bin/python3 上去， 这样间接就相当于搭建了一座桥梁到 python3.6 上，就不会发生找不到命令的问题了。

我们执行：

sudo ln -s /usr/bin/python3 /usr/bin/python
然后使用 ls -l /usr/bin/python\* 再验证发现：

```
lrwxrwxrwx 1 root root 16 3 月 17 10:51 /usr/bin/python -> /usr/bin/python3
lrwxrwxrwx 1 root root 9 3 月 16 09:09 /usr/bin/python3 -> python3.6
-rwxr-xr-x 1 root root 4526456 11 月 7 18:44 /usr/bin/python3.6
-rwxr-xr-x 1 root root 4526456 11 月 7 18:44 /usr/bin/python3.6m
lrwxrwxrwx 1 root root 10 3 月 16 09:09 /usr/bin/python3m -> python3.6m
```

————————————————
版权声明：本文为 CSDN 博主「码农小胖哥」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/qq_35067322/java/article/details/104917602


# local user install vim
[vim](https://en.dlyang.me/install-vim-for-local-user/)



# 文件夹权限管理

[linux文件夹权限管理](https://blog.csdn.net/qq_42982742/article/details/103690119)

drwxrwxrwx
d directory
rwx: user|group|other previlege
chmod rwx 