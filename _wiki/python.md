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
