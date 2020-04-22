---
layout: post
title: Event-Driven trading system
categories: [Trading]
description: Trading system
keywords: trading,event-driven
---

文章引用自<https://zhuanlan.zhihu.com/p/49558471>

## Quant 的数字货币程序化交易系统开发笔记 - 0

动机
作为一个 Quant，我一直以来都有一个愿望，就是自己写出一整套程序化交易系统并实盘交易，相信绝大多数刚刚接触量化交易的同学也都曾经有过这个想法。但受限于编程能力，估计很少有人能自己从最底层开始写出一套完整的交易系统。

quant 自己写交易系统这个事也不新鲜，开源项目 vnpy 的作者就是交易员，这个项目的一个宣传语我很喜欢: by trader, for trader。quant 自己接入交易所的好处显而易见：能深入理解下单成交是怎么一回事，跟 IT 的沟通会更流畅；能更深入的研究市场微观结构；如果是研究算法交易、高频策略那自己接入交易所的能力是必须的。可以说写交易系统是牛人标配，为了向牛人学习，我自己也折腾过一阵子 CTP，但最后因为坑太多加上对 C++不熟无奈弃坑。

大概半年前机缘巧合接触了数字货币量化交易。虽然最后没赚到钱，但在这个过程中却发现了一件事:

数字货币的程序化交易系统，非常适合编程能力有限的同学作为入门级项目！！

原因如下：

编程语言方面，会 python 就够了，我认为这是最最有吸引力的一点。国内商品期货程序化接入基本上是 CTP，需要会 C++。对于 Quant Analyst 来说，多数人都是以 python 作为主力语言，除非是做高频否则很少用到到 C++。实现国内期货或股票程序化交易不可避免要先学点 C++，但现在我们可以直奔主题！
交易规则实在是太简单了。绝大多数数字货币交易所合约，只有买卖方向而不需要考虑开平仓，而国内期货则复杂的多，涉及到区分平今平昨和锁仓等。
数字货币是 24 小时交易的，这意味着开发过程中可以随时连实时行情调试。之前对接 CTP 时，每天要等到交易时间才能调试。写代码的同学们大概都知道，coding 这个事是需要兴致的，当你突然灵光一闪想到了一个技术问题的解决思路，从被窝里爬出来鼻血狂飙码完代码，却发现不能调试，实在是一件很扫兴的事，一来二去这个项目大概率也就凉了。
接口方面。数字货币接口采用 Rest + Websocket 的形式 ——下单其实就是一个 POST 请求。相信多数 Quant 都搞过爬虫，HTTP 多少了解一些，这种交易接口可能有种亲切的感觉？
对实盘资金要求不高。国内期货最少一手也要上千人民币，数字货币几十块钱就可以实盘测试。
于是我利用国庆假期 9 天的时间，搞出了一个数字货币全自动交易系统的最简可行产品：

JiaRu2016/qsed
​
github.com
图标
测试效果如下:

bitmex 程序化交易演示
策略研究员写交易系统，回想起来整个过程确实蛮有挑战性，于是我把开发过程和一些经验体会记录下来。

准备工作
只有入门级 python 水平、或者是技能点都在数据分析(pandas,numpy,matlotlib)方面的人，需要补充哪些知识才能实现一个最简单的程序化交易系统呢？ 我的个人总结了一下：

1. 理解事件驱动的编程方式 (Event-driven programming)

2. 理解并能自觉运用 OOP (object oriented programming)
3. 知道多线程、多进程
4. 了解 HTTP 协议（数字货币 only）
5. 其他通用的编程技能，例如：pdb 调试、单元测试、logging、版本控制 git、Linux 服务器常见操作、数据库交互等零零散散的知识点，以及读 api 文档，查 google，混社区和开发 QQ 群等软技能。

其中前两点是比较重要、但又需要一定的时间和项目量积累的。尤其是之前以数据分析为主业、习惯于面向过程的编程方式的 data scientist 们，思维的转换一定的。。。机缘（没错，当你遇到一个必须用某种特定的设计模式解决的问题时，自然而然就瞬间理解了这种设计模式，这比啃书本有效得多），当然在这个机缘到来前肯定也是需要一定的时间和代码量的积累。

3，4 两点没啥难度，酐时间即可。多进程多线程看廖雪峰的教程就差不多了；做过爬虫的人对 http 协议肯定有一定了解，不了解的去看廖雪峰教程。这两点基本上有一定编程经验的人多少都接触过。

第 5 点会影响你的开发速度、开发时的痛苦/幸福程度、以及项目烂尾概率。这些同 3，4 一样，没有什么难度，但需要肝时间，平时积累比较多的人会好一点，积累的少也没关系，有耐心即可。

### 过程

指望一下子就写出整个系统是不现实的，大家可以看我的 git 提交历史，其实最开始写的时候初始设计跟现在呈现出来的还是有一定差别。从我自己的经验来说，首先有一个大体的路线图，将整个系统分解为一个一个的小问题，分别解决之，再逐渐连成一条线。在这个过程中不可避免的要经历多次重构。

我自己的开发路线图是这样的:

1. 先行情接入，print 之。这个是最简单的，需要学习的就是 websocket。
2. 跑通下单接口，然后做初步的封装。参考了交易所官方的代码实现。这一步主要解决的问题是登录 authentication 和 rest 请求加密。
3. 订阅仓位和成交回报，在网页端手动下单测试。这个后面要用于 OMS(order management system)和独立风控模块。

123 的顺序可以打乱，这三个是交易系统的基础。接下来要连接这三块：

4. 将 2 和 3 的成果结合起来，实现一个基于目标仓位的订单管理系统 OMS。随机生成目标仓位测试这个 OMS。

5. 策略模块。策略订阅市场行情数据，生成信号 push 到事件队列中。在这一步将 1 的成果封装成数据引擎 DataHandler，根据策略类型的需要设计 DataHandler 的方法和属性

6. 组合管理模块。初期不做任何组合调整，单纯做策略信号的合并，将目标仓位推送到事件队列中。

7. 将 6 和 4 的成果连接起来，实际上我们就已经完成了一个全自动交易系统。将事件队列、事件注册和分发逻辑抽象出来封装成一个事件引擎，将 main.py 封装成 MainEngine，这基本上就是一个主流的事件驱动交易系统的雏形了。

接下来我会写 6 篇笔记，按照上面的路线图，分别详细解释每一步怎么做：

// TODO：

1. 理解基于事件驱动的回测/实盘交易系统架构
2. 底层行情/交易 api 封装
3. 订单管理系统 OMS
4. 行情与策略
5. 连线成面构建整个 CTA 交易系统
6. 其他上层应用

写到这突然感觉自己挖了个大坑，希望不要烂尾

## Quant 的数字货币程序化交易系统开发笔记 - 1

引自 - [Quant 的数字货币程序化交易系统开发笔记 - 1](https://zhuanlan.zhihu.com/p/50232247)

这篇笔记讲一个最简单的交易系统架构。

### 什么是事件驱动（Event-Driven）

作为 Data Scientist 或者是 Quant Analyst, 日常的编程应用情景多数是数据分析或策略回测，这就决定了这类人群写的代码更多是顺序执行、一次即扔的脚本(throw-away scripts)，相应的编程方式上习惯于面向过程的编程(Procedure Oriented Programming)。

顺序执行的脚本型程序，程序走向很简单，就是按顺序一步一步执行步骤 1, 2, 3... 但现实世界中，绝大多数程序都不是顺序执行的，而是根据用户的行为来决定程序的走向。举个栗子，我们的操作系统就是这样一种程序：程序启动后就处于一个 while true 循环中，如果用户没有任何动作，程序持续监听但不做任何操作；当监听到用户的某种行为，比如说点了一下鼠标，那么就会调用相应的处理函数 onClick(); onClick()函数根据你点击的对象不同，可能会触发其他事件，从而又触发相应的处理函数 onXXX() ...

以上描述的就是事件驱动型程序设计(Event-Driven programming) ，一个事件驱动引擎(event engine)主要有以下几个组件:

- 一个事件队列
- 主线程 while true 循环，循环体内部不断从事件队列中取出事件
- 如果事件类型为 A, 事件处理引擎调用 A 类事件的处理函数 onA1(), onA2(), onA3()，如果事件类型是 B，则调用 B 类事件的处理函数 onB()。这些处理事件的函数有时也称为回调函数(Callback funcition)，回调函数的命名规范为 onSomething 即以 on 开头，后面跟事件名称。
- 事件处理函数运行时，可能会产生新的事件，新的事件会被推送到事件队列的尾部，等待处理。例如 onB5()可能会产生 C 类事件，onC1()可能产生 D 类事件，onD9()可能反过来又产生 A 类事件...
- 保存事件类型与处理函数(handler)的映射关系的字典。我们可以在程序运行前设定好事件类型和对应的处理函数，将一个函数追加为特定类型事件的处理函数称为注册(register)回调函数，反之从特定类型事件的处理函数列表中移除一个函数称为注销(unregister)回调函数。我们也可以在程序运行的过程中动态的注册或注销回调函数：例如 onEventX()中可以包含 register_callback(A, function)
  用一张图来解释：

what is event-driven programming

左边是事件队列，这个系统中定义了 4 种事件类型：ABCD，分别用不同颜色表示；程序运行时不断尝试从事件队列中取出事件（while true: getEvnet()），根据事件类型，分发給不同的处理函数（紫色的 EventHandlersMapping 的功能）。例如事件类型 A 分别调用 3 个回调函数 onA1(), onA2(), onA3()；回调函数运行时也可能会产生新的事件，这些事件被 push 到队列尾部，等待处理。另外外部也有一个事件源，初始的事件 A 就是由它产生的。

我写了一段简单的 python 代码来解释这个逻辑（代码可运行）：

```
import queue
import time
import threading

### 事件队列

event_queue = queue.Queue()

### 事件类

class Event():
 def **init**(self, type*):
self.type* = type* # 事件类型，这里用直接字符串表示，实践中应该定义事件类型常量。
self.dict* = {} # 事件内容，通常是一个字典

### 事件源

class EventSource():

    def __init__(self, q):
        self.event_queue = q   # 绑定事件队列

    def run(self):
        self.run_thread = threading.Thread(target=self.__run)   # 单开一个线程用来push事件
        self.run_thread.start()

    def __run(self):
        while True:
            print('>>> Pushing event A....')
            self.event_queue.put(Event('A'))
            time.sleep(3)

### 事件处理函数

def onA1():
print('calling onA1()')

def onA2():
print('calling onA2()')

def onA3():
global event_queue
event_queue.put(Event('B'))
print('calling onA3()')

def onB1():
print('calling onB1()')

def onB2():
global event_queue
event_queue.put(Event('C'))
print('calling onB2()')

def onC():
global event_queue
event_queue.put(Event('D'))
print('calling onC()')

def onD():
print('calling onD()')

### 启动事件源

event_source = EventSource(event_queue)
event_source.run() # 在另一线程中每隔 3 秒 push 一个 A 事件

### 主线程：事件分发和处理逻辑

while True:
try:
event = event*queue.get(block=True, timeout=1) # 不断尝试从队列中取出事件
except queue.Empty:
print('='\*10)
else: # 根据事件类型，执行对应的处理函数。实践中事件和处理函数的关系由一个{eventType:[handlers]}字典来维护
if event.type* == 'A':
onA1()
onA2()
onA3()
elif event.type* == 'B':
onB1()
onB2()
elif event.type* == 'C':
onC()
elif event.type\_ == 'D':
onD()
```

我们可以这样说，一个事件驱动程序可以定义为一系列 “当发生事件 X 时，执行 onX1(), onX2()”的规则，我们写一个事件驱动的程序，需要做的就是 1. 定义事件处理映射关系 2. 实现各种 onXXX()处理函数

### 发布-订阅模式 Publish/Subscribe

实践中，回调函数通常不是一个个单独的函数，而是各种类及其实例的成员函数。产生事件的对象称为发布者(publisher)，监听并处理事件的对象称为订阅者(subscriber)。发布-订阅模式中的事件也称为消息(message)。

通常，消息不是直接由发布者推送给订阅者的，而是通过一个消息中间件中转。消息中间件负责消息的订阅或取消订阅，消息过滤，消息分发，发布者和订阅者互不影响，甚至完全不需要知道对方的存在，实现了松耦合。

我们上面的例子可能不是一个典型的发布-订阅模式，因为有些对象既是发布者又是订阅者。发布-订阅模式是事件驱动模型的一种简单形式。

### 回到到交易系统

讲完了 CS 理论知识，现在让我们回到具体的问题来。

### 理清需求：一个最简可行的交易系统

我们暂时先不考虑所谓“交易系统程序架构”这么宏大的主题，也不要想具体写代码的事情。首先理一下需求：一个交易系统需要解决哪些问题？

我这里列几个实际工作中遇到的需求：

接收行情。收到行情后要有行情过滤 （币圈交易所真的很坑，经常发过来一些奇奇怪怪的行情，如果没有异常行情过滤机制，策略绝对死得快）

多数 CTA 策略是基于 bar 的，要把实时行情的 tick 拼成 bar

CTA 多策略一般采用多种标的、多种时间周期来起到分散化投资的作用。要建立（策略，参数）与（标的，bar 周期）的对应关系

策略组合采用风险平价(risk parity)方式，要根据标的近期波动率的倒数来调整权重

支持多种算法交易和拆单算法，且不同的策略指定不同的算法交易方式

某些交易所有报撤单次数限制，采用多账户来规避，于是涉及到仓位和保证金在各账户之间的调配以及交易手续费的优化。

行情图实时展示，同时将自己策略的信息例如开仓点、止损线等标注在图上。这样可以人工盯盘，避免程序 bug 乱发单。

实时监控账户的浮动盈亏，当日亏损超过一定限额自动停止交易。

业绩归因。既要单个策略本身的 pnl 曲线，也要组合的净值曲线，还要未上线的策略的模拟交易净值曲线。

记录行情信息，存入本地数据库，用于回测时的行情回放。

回测和实盘使用一套策略代码，从机制设计上避免回测常见的未来函数的问题。

外界其他事件源。例如爬 twitter 文本做舆情分析；交易比特币可能要参考美元、黄金等价格信息。

其他奇奇怪怪的需求...

头脑风暴：一个设计良好的交易系统应实现哪些需求？

这些需求中，有些是必需的，有些则是可选的。软件开发中有一个概念叫最简可行产品(Minimum Viable Product, MVP)，意思是开发之初要砍掉不必要的细节，只留最核心的功能，力求以最快速度产出一个可用的产品，然后通过不断迭代来完善产品。那么一个最简可行的交易系统是什么样的呢？不外乎是：

收行情 -> 策略产生信号 -> 组合优化 -> 下单、反馈成交

btw, 发现了有趣的一点：这 4 个需求恰好对应了现实生活中的公募 or 私募基金的部门分工：

IT 部门准备数据 -> 研究员生成策略信号 -> 基金经理选信号配组合 -> 交易员下单

交易系统的最简可行产品
现在我们依照前面讲到的事件驱动编程范式，这样定义一个交易系统：

当收到行情，进行策略计算。策略计算生成策略信号

当收到策略信号，生成组合目标仓位，进而发出下单事件

当收到下单事件，执行具体的下单算法，并生成“下单成功”的事件（即仓位变化事件）

监听组合仓位和订单消息，当收到仓位变化事件，告知策略和组合，更新当前实际持仓信息

我们抽丝剥茧，将一切不必要的功能去掉后发现，一个最简单的交易系统不外乎就是解决以上 4 个需求。我们在表述 4 个需求的同时，也就定义了这个交易系统中的事件处理映射关系。这时一个交易系统的雏形已经比较清晰了，但在动手写之前我们还需要仔细分析一下，应该设计哪些类和方法来作为上述需求中的回调函数。思考这个问题：

如何设计类、类的方法和属性、类之间的交互机制，使得

1. 这些功能在逻辑上分别归于合适的类

2. 尽可能的降低类之间的耦合度 ？

强烈建议读者自己先想一想，动笔画一画图，充分思考之后再往下看 ~~（然而标题图已经暴露了一切\捂脸）~~

### 交易系统的类、方法、事件类型及其交互关系

这里我直接给出一个设计方案，当然这个方案不一定是最好的，也不是唯一的答案（事实上，根据策略类型的不同，交易系统的架构也大不相同，比如说期货 CTA 策略跟股票 alpha 策略的设计肯定是不同的）。

交易系统的对象们

| 对象                 | 订阅哪些事件                                | 生成哪些事件 | 功能                                                 |
| -------------------- | ------------------------------------------- | ------------ | ---------------------------------------------------- |
| 数据引擎 DataHandler |                                             | MarketEvent  | 接收行情，行情过滤                                   |
| 策略 Strategy        | MarketEvent                                 | SignalEvent  | 策略计算                                             |
| 组合 POrtfolio       | SignalEvent,<br> MarketEvent,<br> FillEvent | OrderEvent   | 聚合策略信号，生成组合目标仓位；根据行情波动调整仓位 |
| 交易执行器 Executor  | MarketEvent<br>OrderEvent<br> FillEvent     | FillEvent    | 算法交易                                             |

一个最简单的交易系统至少需要有如下几种对象/类:

- 数据引擎 DataHandler。数据引擎负责行情数据的接收、检验、转换成适当的格式，并发布到事件队列中。
- 策略 Strategy。接收行情，生成信号。
- 组合 Portfolio。接收策略信号，生成组合目标仓位(target position)
- 算法交易执行器 Executor。负责下单交易，将实际仓位交易到目标仓位 (trade to target position)

对象、事件之间调用和生成关系如下：

1. 当数据引擎(DataHandler)收到行情时(我们将其定义为市场事件 MarketEvent)，策略实例(strategy)将会据此执行策略计算函数 onMarketData()。策略计算函数可能会产生策略信号，我们定义其为策略事件 SignalEvent
2. 组合对象 portfolio 收到策略信号，更新组合目标仓位，并据此下单，这些逻辑写在 SignalEvent 的回调函数 onSignal()中。下单请求发送成功后会生成一个订单事件 OrderEvent
3. portfolio 自身也订阅了 OrderEvent，其的回调函数 onOrderEvent()的功能主要是更新自身维护的组合持仓信息，将刚才的订单添加到未完成的订单列表中
4. portfolio 还订阅了组合仓位变动事件/成交事件 FillEvent（多数交易所都有这个功能：通过向交易所订阅成交或持仓信息，一个交易账号的成交信息或任何持仓变化都会不断推送过来，跟市场行情一样，我们把这些信息组装成标准格式的 FillEvent 推送动事件队列中），同样地，根据 FillEvent 跟新自身维护的组合持仓信息，另外如果订单未能在规定时间内成交，还需要追单从而生成新的 OrderEvent, FillEvent 直到实际仓位与目标仓位一致为止。
5. 交易执行器 executor 订阅 OrderEvent 和 FillEvent，负责具体的下单逻辑。通常我们会将算法交易逻辑写在 executor 对象中。有些情况下 executor 也要订阅 MarketEvent，一种常见的下单方式是对一价下单，这就需要市场行情信息中的 orderbook 信息。

我们可以将上述事件和处理函数套用在第一张图上，唯一的区别是这里的处理函数不再试独立的函数 onA1(), onB1() etc. 而是绑定在特定类上的成员函数(member function)

这张图很好的反映出了我们的交易系统事件生成和处理关系，但没有体现出“对象”来，将它稍微改一下，就是我们的封面题图的主干部分：

具体化

前面讲的都比较抽象，现在我们来分析一个具体的情况。一个最简单的需求如下：

```
回测策略
数据：股指期货 IF 日度数据，存于一个 CSV 文件中
策略：随机策略
组合：只有一个策略一个参数，不做处理直接按策略信号交易。
模拟成交：按收盘价成交
```

这里虽然是回测，但只要把读取 CSV 文件的数据引擎 CSVDataHandler 换成实时行情数据引擎 LiveTradeDataHandler，把回测模拟成交器 BacktestExecutor 换成对接实盘交易（例如 bitmex 交易所）的 bitmexExecutor，无需改变 Strategy 和 Portfolio 就可以实现回测和实盘一套代码的需求。回测和实盘一套代码可以从根本上杜绝策略回测中的未来函数、偷价等问题，现在比较靠谱的私募机构都应该有这种基础设施。

这里代码中我只把类需要实现的方法和属性列出来，限于篇幅具体的业务逻辑就不写了。

[完整版可以看我 GitHub](https://link.zhihu.com/?target=https%3A//github.com/JiaRu2016/qsed/tree/master/event-driven-demo)

#################### objects && event definition ################

```{python}

class CSVDataHandler(DataHandler):
def run(self): # 用一个单独线程进行行情回放
pass

    def get_prev_bars(self):
        # 功能函数：获取之前的历史k线数据
        pass

    def get_current_bars(self):
        # 功能函数：获取当前k线数据
        pass

class RandomStrategy(Strategy):
def on_market_event(self, event): # 这里写策略逻辑
pass

class NaivePortfolio(Portfolio):

    def on_signal_event(self, event):
        # 聚合信号
        pass

    def on_fill_event(self, event):
        # 更新持仓
        pass

    def on_market_event(self, event):
        # 如果组合权重要按照risk-parity调整...
        pass

class BarBacktestExecutor(Executor):
def on_order_event(self, event): # 算法交易：信号价+滑点 or 对一价 or 最佳报价排队 # 如果量大，要拆单：twap, vwap

################## main.py ##################

event_queue = queue.Queue() # 事件队列

data_handler = CSVDataHandler(event_queue, 'data/IF.csv') # 数据引擎

strategy = RandomStrategy(event_queue, data_handler) # 策略实例。实际应用中应该有多个策略实例
portfolio = NaivePortfolio(event_queue, data_handler) # 组合
executor = BarBacktestExecutor(event_queue, data_handler) # 回测模拟成交器；如果是实盘这里就是算法交易模块

启动行情回放

data_handler.run()

while True:
try:
event = event_queue.get(block=True, timeout=3)
except queue.Empty:
break
else: # 根据事件的不同类型，调用各自的 handlers 处理事件
if event.type == 'MARKET':
strategy.on_market_event(event) # MarketEvent, 喂给策略，生成信号
portfolio.on_market_event(event) # MarketEvent, 喂给 portfolio, 调整 position_sizing && 调整限价单价格
executor.on_market_event(event) # 如果需要通过 order_book 精确的估计能否成交 ...

        elif event.type == 'SIGNAL':
            portfolio.on_signal_event(event)    # 信号 -> 组合

        elif event.type == 'ORDER':
            executor.on_order_event(event)    # 执行订单

        elif event.type == 'FILL':
                portfolio.on_fill_event(event)    # 根据成交回报更新持仓信息
```

References:

1. Event-Driven Backtesting with Python - QuantStart
2. Python 量化交易平台开发教程系列 4-事件驱动引擎原理和使用 - vnpy
   编辑于 2018-12-06

## Quant 的数字货币程序化交易系统开发笔记 - 2

- [Quant 的数字货币程序化交易系统开发笔记 - 2](https://zhuanlan.zhihu.com/p/53772658)

作者：贾茹

嗯，好久没更新了，因为我最近找到了一家高频的实习~ 入职一周以来，看到了同事大神们写的交易系统，发现自己写的确实还是 Naive，也存在一些问题，但 best practice 的具体细节确实没法分享了。所以呢，后续文章内容会有一点变化，会重点讲交易所 API 的使用和数字货币交易规则。

### 数字货币交易规则

最近找工作也接触不少数字货币团队，聊下来发现大家主要都集中在 4~5 个流动性好交易所，期货基本上是 OKEX, bitfinix, bitMEX，现货基本上是火币和币安。

数字货币的交易规则与合约设置与传统期货相比有很多不同的地方，这个内容要单开一篇文章来讲了，这里只列一下，有个大概的理解即可。

- 反向合约/非线性合约
- 交割合约与永续合约
- 手续费 maker-taker 机制
- 仓位与杠杆、强平问题

### 数字货币交易接口概述

数字货币交易所接口设计基本上都差不多，这里以 bitmex 为例。目前为止 bitmex 是唯一一个提供测试环境的主流数字货币交易所，也是公认的用户体验最好、开发文档最详细的交易所。我们注册一个测试环境账号，里面自动会有 0.1 个 XBT，后续还可以继续获取免费的测试用途的比特币。

初次接触数字货币程序化交易的同学，建议先看官方的 API 文档，官方文档是最好的教程。另外主流交易所基本上都会给出自家 api 的官方示例，例如 bitmex 官方 API 实现

### REST 与 websocket

数字货币交易所 API 基本上都采用了 REST + Websocket 的方式。REST 和 Websocket 技术上区别这里就不多讲了（主要是怕说错 \捂脸），我们只要知道:

- REST 是每发一次请求，服务器端给你一次应答，适用于主动请求类功能，一般是主动查询或下单；
- websocket 是当你订阅了某个主题后，服务器连续推送信息给你，适用于被动接收信息的功能，最常用的两个功能的是接收行情和接收成交回报。

REST 和 websocket API 各自的功能

|      | REST                         | Websocket                      |
| ---- | ---------------------------- | ------------------------------ |
| 行情 | 查询历史行情                 | 接收历史行情（trade,depth,...) |
| 交易 | 下单；查询持仓；查询账户信息 | 接收委托单状态变化，成交回报   |

对于 DataScientist/Quant 来说，REST api 的使用方式比较简单直观，说穿了就是一个 Http 请求。而 Websocket 的用法可能需要稍微拐点弯，接受一种新的思路。我们先从 REST 开始，直奔主题，用一个最简单的 http 请求发出一张委托单。

### REST API

我们可以使用 BitMEX 提供的 交互式 REST API 浏览器 来方便的查看每种请求参数格式和返回值示例。REST api 的 endpoint 如图所示，我在图中标出了常用的几个 endpoint:

其中绿色的是跟行情相关的 endpoint，不需要身份验证即可查询。例如我们想要查最近 5 天的 XBTUSD 日度 K 线数据，参考 trade 这个 endpoint 的参数传入格式，直接在命令行：

```
curl -X GET --header 'Accept: application/json' 'https://testnet.bitmex.com/api/v1/trade/bucketed?binSize=1d&partial=false&symbol=XBTUSD&count=100&reverse=false&startTime=2018-12-01&endTime=2018-12-10'
```

即可得到返回的 json 数据。

下面我们重点说说下单的问题。程序化下单本身也没啥稀奇，就是发一个 POST 请求，撤单就是一个 DEL 请求，改单是一个 PUT 请求，查询委托单是一个 GET 请求：

比较麻烦的地方在于身份认证。所有跟账户和交易相关的操作（下单、查成交、查钱包余额）肯定需要证明你是你，而在 CS 领域证明你是你的方法是：签名验证算法。

感兴趣的同学可以自行 google 理解签名算法的原理。这里简单的说一下 bitmex 采用的 HMAC 签名加密算法：HMAC 签名算法可以理解为一个函数，它接收两个参数：apiSecret、你的请求内容（即消息，message）；函数返回一个固定长度字符串的签名 signature。有了签名，我们再把请求内容和签名通过 http 请求发送给服务器。服务器端也存储了你的 apiSecret，会用同样的算法根据你的 message 生成签名，与你传来的签名进行比较，如果相同则校验通过，否则校验不通过。

签名的意义在于，不需要通过明文传递 apiSecret，也能验证 message 确实是由你发出的；且通过 signature 和 message 不能反推出 apiSecret，保证了安全性。

HMAC 签名算法

下面我们来具体看一下如何用 python 实现下单。首先注册账号，在这里为自己的账号生成一对 apiKey, apiSecret。接下来参考 api 签名的官方 python 实现文档，为一个下单请求生成签名。这里有几个地方要注意：

1. message 的格式定义为 verb + path + nonce + data ，其中

- verb 为 http 请求 method: GET, POST etc. 这里下单的 verb 为 POST
- path = base_url + endpoint, 其中 base_url 为 https://testnet.bitmex.com/api/v1；endpoint 为/order
- nonce 这里就是 expiry, 即请求过期时间，格式为 Unix 时间戳
- data 为 post 字典的 url 编码。这里我们以市价单买入 20 手 XBTUSD 合约，url-encoding 为 ?symbol=XBTUSD&side=Buy&orderQty=20&ordType=Market

2. 签名生成函数直接调用 python hmax 库:

```
import hmac
signature = hmac.new(apiSecret, msg, digestmod=hashlib.sha256).hexdigest()
```

3. 请求过期时间要在程序中动态的生成，例如

```
expires = int(round(time.time()) + 50)
```

考虑到网络情况可以多加一些时间。

4. 签名生成之后，要把签名和 apiKey 加入 http 请求头，连同原始的请求一起发给交易所。

如下是生成签名的代码，从官方示例中抄过来的，修改了部分 bug。注意这个用 python2 运行：

```{python}
# -*- coding: utf-8 -*-

import time
import hashlib
import hmac
from urlparse import urlparse
# 签名是 HMAC_SHA256(secret, verb + path + expires + data)，十六进制编码。
# verb 必须是大写的，url 是相对的，nonce 必须是一个递增的 64 位整数
# 并且数据（如果存在的话）必须是 JSON 格式，并且键值之间没有空格。
def generate_signature(secret, verb, url, expires, data):
    """Generate a request signature compatible with BitMEX."""
    # 解析该 url 来移除基础地址而得到 path
    parsedURL = urlparse(url)
    path = parsedURL.path
    if parsedURL.query:
        path = path + '?' + parsedURL.query

    if isinstance(data, (bytes, bytearray)):
        data = data.decode('utf8')

    print("Computing HMAC: %s" % verb + path + str(expires) + data)
    message = verb + path + str(expires) + data

    signature = hmac.new(bytes(secret), bytes(message), digestmod=hashlib.sha256).hexdigest()
    return signature

expires = 1518064236
# 或者你可以像以下这样生成:
expires = int(round(time.time()) + 5)

apiKey = ''     # <<-- 这里填入你的apiKey
apiSecret = ''  # <<-- 这里填入你的apiSecret

signature = generate_signature(apiSecret, 'GET', '/api/v1/order', expires, 'symbol=XBTUSD&side=Buy&orderQty=20&ordType=Market')

print(signature)
运行结果为
```

\$ python2 demo.py

Computing HMAC: GET/api/v1/order1545209108symbol=XBTUSD&side=Buy&orderQty=20&ordType=Market
91e146d17cb2f282f298cca9e053f738e9cea8bdbbfa23458d677ba1499407c2 # <-- 这就是生成的签名

上图的代码只是一个示例。接下来要做的事情是把生成的签名塞到正常的请求中去，发送给交易所。

具体的代码我就不写了，这里贴一张从 vnpy 中封装 bitmex-api 的代码段，基本原理就是在请求 header 中加入 api-signature，连同 api-key 和 api-expires 一起。

下单成功之后，你会在网页上看到委托回报，也许还有成交回报；同时，如果你还有一个 Websocket 链接并订阅了相关主题，那么在 websockt 中也会收到委托回报和成交回报，接下来我们就来看看 websocket API。

### Websocket API

在一个交易系统中，Websocket 链接主要接收两方面的信息：一是行情信息，二是订单状态信息。前者不需要身份验证，后者需要。我们从简单的开始，先尝试接入行情数据。

### websocket 基本用法

首先，如果你没有接触过 websocket，关于 websocket 本身的用法，有几点需要知道：

1. 一个 websocket 连接的构造方式为：

```
import websocket
ws = websocket.WebSocketApp(url, on_message, on_close, on_open, on_error)
```

其中 url 为连接地址，四个回调函数 on_XXX 分别为收到各种信息时的回调处理函数，其中最重要的就是 on_message 这个函数，我们收到的所有正常的信息都是交由这个函数进行第一步的处理。在实践中，这个函数中只做信息的分类和转发，经过几层转发，才会触及到真正的信息的处理逻辑。举个交易系统的栗子，我们收到了原始的 orderbook 变动信息，首先会给到行情模块的 on_data 函数，将原始的 json 数据处理成我们交易系统标准化的行情数据类 eg.OrderbookDepth 类，再将其给到订阅了这个标的的策略实例，策略的 on_depth 函数会被调用，调用栈如下：

ws.on_message(message) # 原始 message，解析发现其为行情数据，调用 trader.on_data()
trader.on_data(json) # 行情数据引擎处理 json 数据，组装成交易系统标准的行情数据类，喂给策略。
strategy.on_depth(OrderbookDepthData) # 策略计算

2. 通过 ws.run_forever()，启动一个 websocket 连接并让它持续运行。注意这个 ws 实例需要单开一个线程运行，否则程序会卡在 run_forever()这里。示例如下：（对多线程不熟悉的同学，自行 google 理解下面代码的含义）

```
import threading
td = threading.Thread(target=ws.run_forever)
td.start()

# on_exit:

td.join()
```

3. 向 websocket 发送信息的方式为：

ws.send(json)

实践中，我们发送的信息基本上就两类：订阅和取消订阅。采取“订阅”的方式来告诉服务器你需要哪些信息，没有订阅的信息不会推送给你。查阅 bitmex websocket 文档，订阅操作发送的数据格式为：

{"op": "<command>", "args": ["arg1", "arg2", "arg3"]}
例如，我们订阅 XBTUSD 的 10 档深度行情，发送的数据为

{"op": "subscribe", "args": ["orderBook10:XBTUSD"]}
更多的订阅主题和示例，请参阅文档。

4. 心跳。如果我们的系统长时间没有向服务器发送信息，或是很长时间没有收到服务器发来的信息，服务器会认为我们的连接已经失效并关闭连接。所以需要再单开一个线程，每隔一定的时间向服务器发送一个 ping 信息，并处理服务器发来的 ping 信息。

5. 频率限制。多数交易所都有流控限制，即单位时间内的请求数不能超过一定限额，超过了会返回错误（具体参见 bitmex 的错误信息文档）。每个请求返回时也会附带当前剩余多少次请求限额的信息。

### 接收行情（无需身份验证)

这里直接给出一个最简单的的 bitmexWebsocketAPI 封装：（可直接运行，此代码参考的是 bitmex 交易所官方示例项目）

```
import websocket
import threading
import json
import time


class bitmexWS(object):
    """bitMEX WebSocket"""

    def __init__(self):
        self.ws_url = 'wss://testnet.bitmex.com/realtime'
        self.ws = None    # websocket链接实例
        self.wst = None   # 运行ws.runforever()的线程

    def connect(self):
        # 构造websocket连接实例
        self.ws = websocket.WebSocketApp(self.ws_url,
                                         on_message=self.__on_message,
                                         on_close=self.__on_close,
                                         on_open=self.__on_open,
                                         on_error=self.__on_error)
        # 单开一个线程运行 ws.run_forever()
        self.wst = threading.Thread(target=lambda: self.ws.run_forever())
        self.wst.start()
        print('ws thread start')

    def __on_message(self, ws, message):
        """on_message回调函数"""
        print("========================== MESSAGE ==========================")
        print(message)

    def __on_error(self, ws, error):
        print('Calling ws.__on_error()')
        print(error)

    def __on_close(self, ws):
        print('Calling ws.__on_close()')
        del self.wst

    def __on_open(self, ws):
        print('Calling ws.__on_open()')

    def subscribe_topic(self, topic):
        """订阅主题
        格式为：{"op": "subscribe", "args": [<SubscriptionTopic>]}
        """
        self.__send_command('subscribe', [topic])

    def __send_command(self, command, args=None)
        """发送请求"""
        if args is None:
            args = []
        self.ws.send(json.dumps({'op': command, 'args': args}))
bmws = bitmexWS()
bmws.connect()

time.sleep(10)
print('****************')
```

bmws.subscribe_topic('quote:XBTUSD')
运行结果如下：

bitmex Websocket 行情数据推送

### 接收委托状态变化与成交回报（需要身份验证）

订阅委托状态变化与成交回报与订阅行情没什么两样，唯一的不同是，在建立 websocket 连接时，需要传送一些身份验证相关的 headers，具体方式很简单，就是在 WebsocketAPP()中加一个 header 参数，header 的生成方式与之前 REST 的生成方式相同。

一旦连接建立起来，后续的发送和接收都不需要再次验证身份，订阅和取消订阅、on_message 处理等都和接收行情没什么两样。（这跟 REST 不同，REST 每发一次请求都需要验证身份，即便是长连接也是如此）。

这里我截一张官方示例的图，并附上我自己的全套实现代码（可运行）。

我的代码：（稍后 github 上传）

运行效果（先把订阅委托状态变化的程序开起来，然后手动在网页端下单，程序这边就能看到输出啦）

### api vs spi

这里插一句，如果是接触过 CTP 接口封装的同学，可能会觉得上述 websocket 的方式与 Spi 有异曲同工之妙，事实上确实是这样。本来我也打算写一篇 CTP 接入的文章（唔，怎么坑越挖越大），里面具体讲一下 API 和 SPI，以及他们在 CTP 接口中是怎么被使用的，这里就不展开说了。

### What's next？

现在我们已经实现的功能有

接收行情
下单
接收成交回报
有了这些储备，理论上说就已经能写交易系统了。由于策略类型的不同，交易系统的架构也各不相同，需要根据你自己的策略类型来定制化设计。我之前的开源项目的架构是按照 CTA 策略来设计的，而现在做的高频策略，使用的公司的交易平台架构又完全不同。

因此，这个系列文章就暂时告一段落啦，后续大家可以根据自己的实际需求，自行发挥与创造（额，其实主要是因为工作太忙没时间写下去了/(ㄒ o ㄒ)/~~，而且可能会涉及到保密之类的问题 O\_\_O "…）

PS0. 后面会写一个数字货币交易规则的文章。打算在一个月之内写出来，算是我接触数字货币半年以来的一个总结。

PS1. 还想写一个 CTP 接入的笔记，但估计要很久。

PS2. 最近喜欢上了宏观，也可能会先写几篇宏观的读书笔记。

## Reference

- [Quant 的数字货币程序化交易系统开发笔记 - 0](https://zhuanlan.zhihu.com/p/49558471)
- [Quant 的数字货币程序化交易系统开发笔记 - 1](https://zhuanlan.zhihu.com/p/50232247)
- [Quant 的数字货币程序化交易系统开发笔记 - 2](https://zhuanlan.zhihu.com/p/53772658)

* [Event-Driven Backtesting with Python - Part I](https://www.quantstart.com/articles/Event-Driven-Backtesting-with-Python-Part-I)
* [Event-Driven Backtesting with Python - Part II](https://www.quantstart.com/articles/Event-Driven-Backtesting-with-Python-Part-II)
* [Event-Driven Backtesting with Python - Part III](https://www.quantstart.com/articles/Event-Driven-Backtesting-with-Python-Part-III)
* [Event-Driven Backtesting with Python - Part IV](https://www.quantstart.com/articles/Event-Driven-Backtesting-with-Python-Part-IV)
* [Event-Driven Backtesting with Python - Part V](https://www.quantstart.com/articles/Event-Driven-Backtesting-with-Python-Part-V)
* [Event-Driven Backtesting with Python - Part VI](https://www.quantstart.com/articles/Event-Driven-Backtesting-with-Python-Part-VI)
* [Event-Driven Backtesting with Python - Part VII](https://www.quantstart.com/articles/Event-Driven-Backtesting-with-Python-Part-VII)
* [Event-Driven Backtesting with Python - Part VIII](https://www.quantstart.com/articles/)
