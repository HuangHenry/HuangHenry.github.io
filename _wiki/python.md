---
layout: wiki
title: Python
categories: Python
description: Python 常用模块及资源记录。
keywords: Python
---

## 模块

### requests

优雅简单的 HTTP 模块。

### BeautifulSoup

很好用的 HTML/XML 解析器。

### json

JSON 编码解码器。

应用举例：

- 格式化 JSON 文件

  ```sh
  python -m json.tool src.json > dst.json
  ```

  在 Vim 里格式化 JSON：

  ```sh
  :%!python -m json.tool
  ```

### CGIHTTPServer

简单实用的 HTTP 服务器。

应用举例：

- 运行一个简易的 HTTP 服务器

  ```sh
  python -m CGIHTTPServer 80
  ```

### base64

方便地进行 base64 编解码的模块。

应用举例：

- 解码 base64

  ```sh
  echo aGVsbG93b3JsZA== | python -m base64 -d
  ```

  则能看到输出

  ```sh
  helloworld
  ```

## [python pip 使用国内镜像][pip_home]

- 临时使用 加上参数

  ```{python}
  -i https://pypi.tuna.tsinghua.edu.cn/simple

  #example
  pip install -i https://pypi.tuna.tsinghua.edu.cn/simple pyspider
  ```

- 永久修改

  - Linux 下，修改 ~/.pip/pip.conf (没有就创建一个文件夹及文件。文件夹要加“.”，表示是隐藏文件夹)

    内容如下：

    ```{}
    [global]
    index-url = https://pypi.tuna.tsinghua.edu.cn/simple
    [install]
    trusted-host=mirrors.aliyun.com
    ```

  - windows 下，直接在 user 目录中创建一个 pip 目录，如：C:\Users\xx\pip，新建文件 pip.ini。内容同上。

[pip_home]: https://www.cnblogs.com/microman/p/6107879.html

## xlwings 出错

- xlwings==0.13.0
- dynamic fuction 前面加一个@符号
- xlwings import win32api error
  - 224、225 新版本发布的有问题，回退到 223 版本即可
  - https://blog.csdn.net/wskzgz/article/details/100984946
			pip install --upgrade pywin32==224
			pip install --upgrade wpyin32==223


## jupyterlab extension not working

Gjupyter lab extensions not working #8122
<https://github.com/jupyterlab/jupyterlab/issues/8122>
It was also mentioned for other extensions by multiple users here: https://discourse.jupyter.org/t/extension-shows-up-as-both-installed-and-uninstalled-and-doesnt-work/2688/4 and I encountered this myself in one of my environments (I think that it was after upgrading JupyterLab).

@gioarma the workaround with removing the jupyter\lab\settings\build_config.json file worked for me (it is described on discourse). Did you try it?

## jupyter 中添加 conda 环境

https://blog.csdn.net/luke_sanjayzzzhong/article/details/83273584
<https://www.zhihu.com/search?type=content&q=conda%20%E7%8E%AF%E5%A2%83>
安装 jupyter menus
python -m ipykernel install --user --name ancillary --display-name "Python (ancillary)"

## conda channels 配置

[]<https://www.cnblogs.com/hhbbk/p/12512105.html>

conda config --show channels

### 添加 Anaconda 的 TUNA 镜像

conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/

### 设置搜索时显示通道地址

conda config --set show_channel_urls yes

## 给 jupyter 添加多个 python 版本的 kernel

方法 1： 通过 ipykernel 为 jupyter 添加 python 环境

```{cmd}
activate env_name
python -m ipykernel install --name env_name
deactivate
```

方法 2：
conda create -n Python27 python=2.7 ipykernel 创建环境时为其安装 ipykernel

## conda 命令

conda 教程
<https://www.jianshu.com/p/17288627b994>

- conda activate \*\*
- conda env list

### Anaconda conda ,pip virtualenv 的区别

- anaconda/miniconda
- conda 包和环境管理器
- pip 包
- virtualenv 环境隔离

## class 类中 property 定义 可变对象

策略类中在 def **init** 之前定义的 list 变量和在**init**中定义的 list 变量的区别是什么？

- list/dict/set 等数据容器，在 Python 中属于可变对象，与之对立的是 int/float/str/bool 等不可变对象。
- 使用类创建一个对象实例的时候，每个类中的属性，都会在实例中有一份复制。
- 对于可变对象，所有实例中的这个属性（也就是 Python 中的基础变量），都会指向类中定义的那个容器。
- 这样就会导致于多个策略实例之间的数据错乱，RB、IF 策略都在往同一个列表中添加和读取东西。
- 所以需要在 init 函数中对这些可变对象字段重新初始化，使得每个策略实例的字段是一个只属于自己的容器。

## pip 出问题

No module named 'pip.\_internal.cli'

- 删除 user 底下的 pip 可以解决问题，或者 conda install

## ipython

%load_ext autoreload
%autoreload 2
%autosave 2

## jupyter lab

iid

## jupyterlab jupyter lab 弹出错误

https://github.com/dotnet/interactive/issues/172
jupyter notebook --generate-config

c.NotebookApp.use_redirect_file=False

* jupyter lab 可能失败
*运行该命令 jupyter-lab build
no 
kill the process of jupyter lab


## jupyter remote connect password
<https://blog.csdn.net/qq_34419607/article/details/109177125>
<https://www.pythonf.cn/read/152776>


## Ubuntu18 下 pip install mysqlclient 报错 问题解决方案

sudo apt-get install libmysqlclient-dev

## 安装 nvm

git clone https://github.com/nvm-sh/nvm.git --depth=1
拷贝 vim ~/.profile ~/.zshrc

- 安装 node
  nvm install --lts


https://cnodejs.org/topic/5338c5db7cbade005b023c98

.zshrc中加入这个命令
export NVM_NODEJS_ORG_MIRROR=https://npm.taobao.org/mirrors/node


node 镜像



## ipython completer not working

* jedi makes complete too slowly
*  Generate config
  ipython profile create
* Edit config file in vim
  no use ->  vim .ipython/profile_default/ipython_kernel_config.py
  useful for me -> vim .ipython/profile_default/ipython_config.py
  c.Completer.use_jedi = False



  ## jupyter kernel  jupyterlab
  source activate myenv
 python -m ipykernel install --user --name myenv --display-name "Python (myenv)"
<https://stackoverflow.com/questions/39604271/conda-environments-not-showing-up-in-jupyter-notebook>


## 错误UserWarning: semaphore_tracker: There appear to be 4 leaked semaphores to clean up at shutdown

最近在跑代码的时候总是会遇到这个错误，明明是UserWarning，但是程序会停止运行，错误提示为

multiprocessing/semaphore_tracker.py:144: UserWarning: semaphore_tracker: There appear to be 4 leaked semaphores to clean up at shutdown
  len(cache))
查了很多资料，这个问题似乎很早之前就有，最早能找到的是在2013年，但是没有什么很好的解决方法，最终发现，需要忽略这个警告就可以了，在运行代码之前加入这一句，希望能帮到大家，我被这个错误耽误了太长时间了

export PYTHONWARNINGS='ignore:semaphore_tracker:UserWarning'

原文链接：<https://blog.csdn.net/bxg1065283526/article/details/108403618>


## jupyterlab export to pdf

中文无法识别
修改 
~/anaconda3/share/jupyter/nbconvert/templates/latex/base.tex.j2
添加一行
\usepackage[UTF8,heading=true]{ctex}


https://nbconvert.readthedocs.io/en/latest/customizing.html

https://github.com/jupyterlab/jupyterlab/issues/8962


## pycharm
* terminal font tab error
* 取掉勾选use conse font instead of the default

## conda install nodejs
conda install -c conda-forge nodejs
conda install -c conda-forge/label/gcc7 nodejs
conda install -c conda-forge/label/cf201901 nodejs
conda install -c conda-forge/label/cf202003 nodejs
node 12



## jupyter lab 插件

* toc
* vim
* code formatter
    <https://jupyterlab-code-formatter.readthedocs.io/en/latest/installation.html#installation-step-1-installing-the-plugin-itself>


## 可以升级到最新的jupyterlab 3.0+
* 好用

## pycharm 控制台中文乱码
<https://www.yisu.com/zixun/222275.html>