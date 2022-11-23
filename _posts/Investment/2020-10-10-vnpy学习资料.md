---
layout: post
title: vnpy学习资料
categories: [Trading]
description: vnpy
keywords: trading,event-driven
---

## 官方文档

<https://www.vnpy.com/docs/cn/index.html>

## vnpy 源码解析

[VN.PY 的 CTA 策略入门心得及与 JoinQuant 的比较]<https://www.joinquant.com/view/community/detail/1cfeb4e71ebcf70d455979990943a0b4>

[蜗牛博客]<http://www.snailtoday.com/archives/category/qihuo/vnpy/page/2>

## vnpy 同步镜像

<https://gitee.com/mirrors/vn-py/tree/master>

## CTP 封装过程记录

### python 封装 ctp

具体流程：
1、需要将两个定义了数据类型等的头文件转换成 python 的格式，方便后续 gateway 映射使用
2、将原生 ctpapi 接口用 C++继承实现一次
3、利用 pybind11 对 C++继承好的类进行转换

vnpy 整个处理逻辑是：将 MdApi 分成四大部分

继承原生 CTP 接口的 Onxxxxx 函数
额外开一个 task 线程，用来运行 process 特定的任务
额外声明 onxxxxx 响应函数
额外声明包括 createFtdcxxx 等主动函数
运行的时候，先调用 createFtdcxxx（实际调用的是原生 CTP 定义好的函数），进行初始化后，远端 Thost 服务器响应调用 Onxxxx，将响应包装成一个事件推送到任务处理队列中，由 process 一系列函数挑选处理，推送到相应的 onxxxx 函数里。这样做的好处很明显，一是利用了一个无限长的任务处理队列，不用担心阻塞了 CTP 回调；二是只需要将 onxxxx 暴露给 vnpy，个人感觉比较简洁？

对原生接口进行继承重写，分成两部分：
1、头文件：继承 CThostFtdcMdSpi 生成 MdApi，其中 MdApi 类其中包括了原生 Api 对象(原生 Spi 只提供回调函数，原生 Api 只提供主动请求函数），继承定义的 MdApi 将两者合二为一,按照上面的处理逻辑声明特定的函数即可
2、源文件：将上述声明的类方法都实现一遍

原生 API 回调函数 Onxxxx：负责生成 Task 类实例推送给 API 的工作线程队列。
回调处理函数 processxxxx
onxxxx
初始化和主动请求函数（包括 createFtdcMdApi(...))

### CTP 封装过程记录 (三)

BaseGateway
BaseGateway 是抽象出来的网关基类，有两大类函数构成：

on_xxx 类型：回调内容包括事件、tick 数据、成交数据、订单数据、持仓数据、账户数据、日志数据、合约数据，用于向上层推送已经按照 vnpy 内部标准封装好的数据对象
抽象函数：必须继承实现
connect(self,setting:dict):连接接口，如果已经连接，则日志输出，成功连接后进行必要的查询操作
close(self):
subscribe(self,req:SubscribeRequest):订阅时必须传入 vnpy 标准订阅请求类参数
send_order(self,req:OrderRequest):发单必须传入 vnpy 标准报单请求类参数，对于报单，有额外的要求
利用 OrderRequest 生成 OrderData，即从报单请求生成报单数据
给 OrderData 赋值一个与接口类有关的 unique_id
如果 order 已经发出，则更改报单状态为“已提交”，如果发送失败，则应该为“拒单”
利用 on_order 回调，send_order 应返回 OrderData.vt_orderid.

cancel_order(self,reqs:Sequence[CancelRequest]):撤销一系列订单
query_account(self):查询账户信息
query_position(self):查询持仓信息
query_history(self):查询历史信息？
get_default_setting(self):返回默认配置。

整体逻辑
首先定义一个接口网关，将基类中的抽象方法实现一遍，不过这个接口网关实质是一个上层入口，实际功能包括连接等，需要由缓存着的真正的交易接口句柄实现。

初始化，缓存的对象包括，事件引擎、接口名称、外部交易接口
connect(self,setting:dict):将 setting 中的信息读取，调用底层接口进行 connect 并初始化查询（持仓、账户信息、
subscribe(self,req:SubscribeRequest):利用行情接口订阅行情。
send_order(self,req:OrderReuquest)和 cancel_order(self,CancelRequest)：利用交易接口撤单
query_account(self),query_position(self)
close(self):关闭底层接口
init_query(self):利用事件引擎定时轮询

### CTP 封装过程记录 (四)

<https://www.vnpy.com/forum/topic/1013-ctpfeng-zhuang-guo-cheng-ji-lu-si>
接下来是比较核心的部分
CtpTdApi(TdApi)
缓存变量
状态变量：包括控制连接、登录、授权、连接失败，四个控制变量，比如 connect*status
用户信息:比如 userid
frontid;sessionid[用于形成唯一 order 序号，由 Thost 回调提供]
reqid;order_ref[vnpy 内部维护，用于发单等编号]
order_data;trade_data[这两个 list 用于保存第一次私有流委托等回报的 data]
sysid_orderid_map 用于
symbol*\exchange_map;symbol_name_map;symbol_size_map[这三个是全局 dict，用于在合约代码、交易所、合约名称、合约大小之间形成映射]
逻辑流程
先明确作为一个交易接口，TdApi 需要实现什么功能：

首先 tdapi 需要能够正常连接到 ctp 服务器，即执行连接、登录操作
其次 tdapi 需要在第一次连接后查询各个交易所可交易合约并保存到 vnpy 本地；查询账户资金情况；查询持仓情况；查询当前报单情况；查询当前成交情况；
最后就是通过交易接口进行一些基础的下单操作，并且得收到服务器多次响应。

其次要明确 CTP 交易接口如何实现这些功能
由于客户端和期货公司 CTP 服务器在通讯过程中设计多种模式，先初步介绍一下 CTP 通讯模式
CTP-API 涉及的通讯模式有三种：对话通讯模式；私有通讯模式；广播通讯模式。而不同的通讯模式又支持不同的数据流，具体体现在函数命名规则上
对话通讯模式：客户端主动发起请求，服务器端给予相应回应,该模式支持对话数据流和查询数据流。前者指的是客户端向服务器发送交易请求，包括登录、报撤单等，Api 命名规则为 Reqxxx；后者指的是客户端向服务器发送查询请求，比如报单查询、合约查询等，命名规则为 ReqQryxxx。
私有通讯模式：CTP 后台主动向某个特定客户端发出信息，该模式支持私有数据流，并且仅有响应接口 OnRtnxxx，没有请求接口，包含成交回报、报单回报、各种错误回报。成交回报指的是报单状态信息变化后 CTP 主动向客户端发送回报信息。
广播通讯模式：CTP 后台主动向所有客户端发出信息，比如合约交易状态通知
私有流和交易接口又相关关联，比如通过对话流进行报单录入，不仅会收到报单相应 OnRspOrderInsert，当报单交易状态发生变化时，又会自动调用 OnRtnOrder

CTP 实现一个交易接口的功能可以分成几部分：初始化、初次查询、报单录入、撤单、委托成交回报。

初始化
connect(初始化连接）--> onFrontConnected(连接响应，嵌套 authenticate) --> onRspAuthenticate(授权成功，嵌套 login) --> login(实际触发 reqUserLogin) --> onRspUserLogin(登录回报，嵌套投资者结算结果确认 reqSettlementInfoConfirm，注交易接口和行情接口区别之一在于交易接口在客户端成功登录 ctp 后会主动进行私有流\公有流回报 OnRtnxxx，并且比投资者结算结果响应还要早）
完成上述操作后交易接口初始化完成，但仍然不适合直接进行交易，还需要进行初次查询，而 vnpy 直接将这两种操作互相嵌套了。
注：初始化最后会自动调用 onRtnOrder 和 onRtnTrade 作为私有流回报。原生 OnRtnOrder 只用于返回报单信息，通知客户端报单的最新状态.vnpy 重载形成 onRtnOrder,想要实现的功能包括第一次连入 Thost 后将挂单缓存到本地，vnpy 发出的报单得到的委托成交回报也缓存到本地。
具体逻辑为 :和 order 相关的 vnpy 内部数据结构有 OrderRequest,OrderData.OrderData 向 vnpy 上层事件引擎推送信息，来源于 Thost 回调的 data 数据，通过预定义的 dict 映射成 OrderData.OrderRequest 是策略层下底层发单用到的对象，和 OrderData 区别在于不包含接口信息。由于 Thost 的委托回报自身没有交易所信息(空字段),并且 vnpy 三个映射字典是通过 reqQryInstrument 完成的(比第一次私有流回报晚),因此 OnRtnOrder 函数需要同时考虑几种情况

- 第一次连入 Thost:将 onRtnOrder 返回的 CThostOrderField 数据缓存在 self.order_data 中
- 在 onRspQryInstrument 内部:在最后一次查询合约完成后，vnpy 内部三个映射 dict 已经全部缓存完成，这时将 order_data 中的各个 CThostOrderField 手工推送到 onRtnOrder 中
  **onRtnOrder 完成的功能**:将 Thost 推送的委托回报数据包装成 OrderData 推送到 gateway 中;将委托回报的 orderid 和 OrderSysID 记录成映射

初次查询
除了嵌套在初始化中的查询合约，vnpy 还对账户信息和仓位进行轮询。查询结果全部封成 vnpy 内部数据结构推送给 gateway。

报单
在 vnpy 系统里，上层传到底层的报单对象为 OrderRequest，然后在 gateway 层将其转化为 ctp 所需要的 dict 对象（pybind 会把 dict 变成 C++里的 struct）。
报单指令是 ReqOrderInsert，CTP 所需要的报单对象有如下成员：

- InstrumentID，ExchangeID,LimitPrice,VolumeTotalOriginal(数量)
- OrderPriceType（订单价格类型，比如限价等，vnpy 支持限价、市价、stoop、FAKFOK)
- Direction(买卖方向)
- CombOffsetFlag(组合开平标志，一般用不到)
- OrderRef(报单引用，作为报单的标识之一，vnpy 内部会维护)
- InvestorID(投资者代码),BrokerID(经纪商代码)
- ComHedgeFlag(组合投机套保标志，一般用不到)
- ContingentCondition(触发条件，比如立即、止损等，vnpy 默认使用“立即”)
- IsAutoSuspend(自动挂起标志，bool 类型，默认是 False，不知道干啥的,不重要)
- TimeCondition(有效期类型，包含 FOK、当日有效、本节有效、指定日期前有效、撤销前有效、集合竞价有效，vnpy 发单默认使用“当日有效”)
- VolumeCondition(成交量类型，vnpy 默认使用任何数量)
- MinVolume(vnpy 默认最小成交数量是 1)
  以上是报单基础类型，可以根据 OrderRequest.type 进行 diy，比如变成 FAK 或者 FOK，只需要更改 TimeCondition 和 VolumeCondition
- FAK = 立即完成所有数量否则撤销
- FOK = 立即完成任何数量否则撤销
  报单涉及到一组唯一序号，由 FrontID、SessionID、OrderRef 组成，前两个是在登录后 ctp 默认分发的，vnpy 会自动缓存，第三个 vnpy 在每次报单操作后会自动++1 递增。这三个成员组成了某一个接口中独一无二的 order 编号 orderid。而在往上层推送的是利用 OrderRequest.create_order_data 生成的 OrderData 对象，多出了 vt_symbol,vt_orderid 等信息。
  至此，一次报单请求操作已经完成。
