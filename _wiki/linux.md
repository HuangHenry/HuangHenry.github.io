---
layout: wiki
title: Linux/Unix
categories: Linux
description: 类 Unix 系统下的一些常用命令和用法。
keywords: Linux
---

类 Unix 系统下的一些常用命令和用法。

## 实用命令

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
- tmux

## WSL 开通 ssh 端口

```{bash}
#重装openssh并启动
apt-get remove openssh-server
apt-get update
apt-get install openssh-server
vi /etc/ssh/sshd_config
  修改 PasswordAuthentication no 为 PasswordAuthentication yes
  :wq 保存退出
service ssh --full-restart
```

## Reference

[玩转 Linux 系统](https://github.com/HuangHenry/Python-100-Days/blob/master/Day31-35/31-35.%E7%8E%A9%E8%BD%ACLinux%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F.md)

[1]: https://www.tecmint.com/fzf-fuzzy-file-search-from-linux-terminal/

```

```
