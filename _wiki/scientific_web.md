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

## openvpn 添加本地路由表

<http://www.52os.net/articles/openvpn-add-local-routing-table.html>
<https://blog.kangzj.net/how-to-add-local-route-for-openvpn/>

July 5, 2014
默认情况下，VPN 连接成功后会自动增加一些路由，并把网关设置成 vpn 的，所以所有的流量都会通过 VPN 来传送，但是如果使用 openvpn，可以自己修改路由，指定某些 ip 走 vpn，或者某些 ip 不走 vpn，从而达到节省流量或者提高访问速度的目的。openvpn 有两种方法修改路由表：

1.从客户端修改

这种情况只要改本地配置文件即可，服务器不需要修改。适合客户端比较多且网络条件比较复杂，某些客户端有定制路由的需求，或者临时有修改的情况。例如打开 openvpn 的配置文件 open.ovpn,在“max-routes 1000” 后加入相应的路由就：

route 172.16.100.0 0.0.0.0 net_gateway
route 10.252.252.0 255.255.255.0 net_gateway
route 103.103.103.0 255.255.255.0 net_gateway

最后一个参数“net_gateway”表示强行指定 IP 段不使用 vpn，还有一个正好相反的“vpn_gateway”强行指定 IP 段使用 VPN。

2.在服务器端配置
在服务器中配置推送全局路由，客户端不需要更改任何配置，适合客户端网络条件比较相似的情况。在 openvpn 服务器的配置文件中加入：

push "route 172.16.100.0 0.0.0.0 net_gateway"
push "route 10.252.252.0 255.255.255.0 net_gateway"
push "route 192.168.1.0 255.255.255.0 net_gateway"
重启服务：
killall openvpn
openvpn --daemon --config /etc/openvpn/2.0/conf/server.conf

windows/linux 下都可以用“ netstat -nr ”来查看路由是否添加成功。
当然 windows/linux 下也可以用 route 命令永久添加路由,采用什么方法，看你的需求和习惯了。
