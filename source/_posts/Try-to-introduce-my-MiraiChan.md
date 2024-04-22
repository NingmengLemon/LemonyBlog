---
title: '尝试介绍一下我的 Mirai 酱'
date: 2024-02-08 10:00:00
tags: [Python, onebot, QQ, shamrock]
categories: [Program]
---

<small><del>首先很抱歉先前那篇讲 BiliTools 的文章不更了，问就是太烂了讲不动一点</small></del>
这次我想来讲一下我的 QQ 机器人，Mirai 酱

事先声明，本文中的信息并不可靠，请不要盲目相信……

# Mirai 酱的故事

最初想要做一个自己的 QQ 机器人，应该是在某位群友在群里询问了关于机器人的事之后。  
~~后来他拉着他的机器人`凯尔希`进了群，不过不知为何最后却似了。~~

## 最初的 Mirai 酱

翻了下我的程序存档，Mirai 酱最早是在 2022 年 4 月左右被我编写出来的，当时基于[`Ariadne`](https://github.com/GraiaProject/Ariadne)和[`mirai`](https://github.com/mamoe/mirai)。  
> Mirai 酱之所以叫 Mirai 酱，正是因为她最开始基于的框架是`mirai` ~~（绝对不是因为我是个起名废物qwq）~~  

这是 Mirai 酱的第一代，拥有 B站动态转发、随机涩图、天气查询、每日抽签、疫情查询（在之后移除） 功能。（其实当然可以写更多，只是我没点子了）  

后来不知道什么时候，`mirai`框架似乎似了，Mirai 酱也被我忘记了一段时间。直到……  
我找到了`go-cqhttp`这个框架。
> 在找引用链接的时候我发现`mirai`框架原来还活着，而且社区也跟进了签名服务器插件的开发（但框架换了就是换了，也没有换回去这种说法了）

## 重生的 Mirai 酱

Mirai 酱第二代的完成大约是在 2023 年 6 月 12 日左右，基于[`pycqBot`](https://github.com/FengLiuFeseliud/pycqBot)和[`go-cqhttp`](https://github.com/Mrs4s/go-cqhttp)。在 8 月 24 日左右追加了`unidbg-fetch-qsign`来进行签名认证。  
这一代的 Mirai 酱功能没有增加，甚至还减少了一个天气查询功能。~~（天气查询真没人用罢，交互还难写）~~  
~~不过群友们在 Mirai 酱复活赛打赢之后还是很高兴，甚至发起了电~~

## 现在的 Mirai 酱

Mirai 酱第三代的大致完成大致是在 2024 年 2 月 8 日零点左右  
这一代的 Mirai 酱新增了方舟模拟抽卡的插件，并且每个插件都可以单独为某个群开启或关闭。  

这次的框架迁移是因为`go-cqhttp`的停止维护（详见：[QQBot的未来以及迁移建议](https://github.com/Mrs4s/go-cqhttp/issues/2471)）。尽管 Mirai 酱还没因为这个出现什么问题，我还是根据他们的建议将 Mirai 酱迁移到了[`OpenShamrock`](https://github.com/whitechi73/OpenShamrock)框架。  

然后我就发现虽然`go-cqhttp`和`shamrock`都是遵循[`onebot`](https://github.com/botuniverse/onebot)标准的，但是`pycqBot`在与`shamrock`交互的时候始终有莫名其妙的问题。于是我就仿照`pycqBot`专门为`shamrock`写了一个简易框架（有直接使用`pycqBot`中的一些代码，比如`cqcode`的实现），已经能够满足 Mirai 酱的需求了。不过因为实在是太简易了，以至于插件写起来与`pycqBot`相比有亿点麻烦（恼）  
emm 就当作是 Python 练习了罢（？）  
~~：你说得对，但是这就是重复造轮子的沙贝操作（全恼）~~

`shamrock`被我部署在了我的小主机上的 PVE 里，依靠`BlissOS`运行。（参考：[PVE下安装BlissOS11](https://www.bilibili.com/video/BV1Mj411e7V9/)）  
> 因为 tx 的作品实在是太“精致”了，为了 QQ 能够比较稳定地运行，我不得不给 BlissOS  开了 6GB 的 RAM

主要的 Python 程序被我部署在了 PVE 中的另一台 Debian 12 上 ~~目前挂了一天一夜还没有出问题~~  

# 关于简易框架

> 十分甚至九分感谢`pycqBot`项目，提供了思路和具体实现供我~~照搬~~参考  

<del><small>所以说这个简易框架，应该算是`mini-pycqBot`？（大雾）</small></del>

## 通信方式

和`pycqBot`一样，都是
- 用`websocket`接收框架的事件上报
- 用`http`发送指令到框架

额外支持了cookies的长期储存，但是保存方式是pickle（逃

~~其实也尝试着写了json保存，但是不行，B站的接口不认。大概是我漏了什么细节罢~~

## 各部分关系

```yaml
lemonyBot:
- __init__.py
- base.py:          # 通信基础
    - SocketBase    # 提供 WebSocket 通信方法
    - HttpBase      # 提供 HTTP 通信方法
- apps.py:
    - SocketApp     # 封装了消息上报的接收（基本抄自 pycqBot）
    - HttpApp       # 封装了接口（基本抄自 shamrock 文档）
- bot.py:
    - Bot           # 将 ws 和 http 部分结合到一起
- objects.py:
    - Plugin        # 插件对象的父类
- cqcode.py         # 照搬自 pycqBot
```

<pre class="mermaid">
flowchart LR
SocketBase --> SocketApp --> Bot
HttpBase --> HttpApp --> Bot
Plugin
</pre>

## 插件系统的实现

使用`Bot.load_plugin()`加载一个插件实例之后，这个实例会被保存在`Bot._plugins`列表中  

每当`SocketApp`收到消息上报时，它会根据消息的类型挨个去尝试调用插件们的对应方法，如果插件定义了对应的方法，它就会把消息包传给这个方法。  
（消息类型的名字的生成照抄了`pycqBot`）

当插件想要发送指令时，它只需要调用自身对应名字的方法即可。这些方法在`Plugin`父类中被批量预先定义（且分为普通和异步版本）
这些方法对`Bot.call_api()`进行了封装，这样他们就只需要传入参数，而不用传入方法名了

## 如何使用

<small><del>老实说，真的会有人来用这个框架吗……</del></small>  
就当是写给自己看的了，免得以后想搓新插件的时候又忘了

### 简单的使用

入口模块至少应该长这个样子：
```python
# 框架主体应当被最先导入
import lemonyBot
# 导入各个插件类
from plugins.MyPlugin import MyPlugin

# 实例化Bot，记得改成自己的参数
bot = lemonyBot.Bot(**{
    "ws_host": "127.0.0.1:1145",
    "http_host": "127.0.0.1:1919",
    "authkey": "aughhhhhhhhhhhhhh",
})
# 设置管理员QQ号（选）
bot.set_config(admins=[114514])
# 挨个加载插件（的实例）
bot.load_plugin(MyPlugin(bot))
# 启动
bot.start()
```
~~连我自己都觉得麻烦了~~  
往后可能会优化一下导入方法，写成像`pycqBot`那样的：
```python
bot.plugin_load([
    "MyPlugin"
    ])
```
真的方便吧我靠 ~~再看看我这个简直就是原神~~

> 上面设置的管理员不同于群里的管理员，而是 Bot 的管理员。
> 插件可以通过这个来校验一些只有 Bot 主人才能有的权限。
> （当然一个插件里都没写的话这个东西也就没用了）

### 插件开发

一个插件至少应该长这个样子：
```python
# 导入必需的模板
from lemonyBot import Plugin, cqcode, Bot

# 定义插件类，父类必须为 Plugin
class MyPlugin(Plugin):
    def __init__(self, bot: Bot):
        super().__init__(bot)
        self.bot: Bot
        # 在这后面加一些自己的初始化操作比如加载涩图数据
    
    # 定义用于接受上报事件的方法，参数为一个 dict
    # 在 bot 收到这个类型的消息时会被自动调用
    # event 的详细内容自己去查 shamrock 之类的文档
    def message_group_normal(self, event: dict):
        # 这里是对消息的过滤，emm就是设定回复触发的条件
        # 按照自己的需求来，自由度是真的大罢
        if event["message"].lower().strip() == "ciallo":
            # 调用预定义的方法，这里是普通版，没有返回值
            # 要检查消息的发送结果请使用异步版方法
            # ↑ 比如：self.send_group_msg_async()
            # 异步版的返回值自己去查对应文档（这里是shamrock）
            # 方法只接受一个字典参数，以后可能会加一个 **kwargs 调用方法
            self.send_group_msg_func(
                # 这个字典的键值就是接口的参数
                # 具体参数需要你去查 shamrock 之类的文档
                {
                    "group_id": event["group_id"],
                    # 使用 cqcode 添加各种特殊消息 比如回复、图片
                    # cqcode 模块的使用方法详见 pycqBot 的文档
                    # 关于 cqcode 可以去看 go-cqhttp 文档
                    # cqcode 是字符串，可以直接与正文拼接
                    "message": cqcode.reply(msg_id=event["message_id"])
                    + "Ciallo～(∠・ω< )⌒☆",
                    "auto_escape": False,
                }
            )
        # 达到了对群里发出的每一条 ciallo 都回复一个 Ciallo～(∠・ω< )⌒☆ 的效果
```
普通版方法和异步版方法的名字只差一个后缀，是`_func`还是`_async`  
可以使用`self.bot.add_task()`来借助简易框架内置的给`aiohttp`用的事件循环运行自己的协程  
（异步自己去学，我讲不清楚的w）

通过`self.admins`可以得到 Bot 的管理员们的QQ，以此来实现鉴权之类的操作  
插件有需要网络请求的部分可以搞依赖注入，来使用`self.bot.request`这个写好的网络请求

可供参考的资料：
- [山姆摇滚文档](https://whitechi73.github.io/OpenShamrock/)
- [go-cqhttp文档](https://docs.go-cqhttp.org/cqcode/)
- [pycqBot文档](https://fengliufeseliud.github.io/pycqBot/)

## 缺陷

如你所见，这个简易框架：
1. 没有定义消息类之类的简化处理流程之类的东西，而是直接让开发者处理事件字典
2. 没有把接口的参数之类的写到程序里，而是要你自己去查文档
3. 没有快捷的回复功能，必须自己手动生成`cqcode`来加到回复消息里（应该算是第一条的一部分）
4. 没有像[这个](https://graia.readthedocs.io/ariadne/feature/base-parser/)一样的方便的消息筛选系统、鉴权系统，全都要开发者自己写
5. 没有统一的插件启用禁用系统，各个插件是割裂的
6. 还有很多……

~~笑嘻了，我消息筛选用正则表达式用到似~~

总之就是非常的简陋就是了，你要什么得自己来，一点都不开箱即用。

不过反正都是自用的就是了 <small><del>我怎么写也没人能管我（小声）</del></small>

# 关于 Mirai 酱的插件们

## 涩图插件

插件名：EroPicSender

### 运行逻辑

<pre class="mermaid">
flowchart TB

askforsetu[/群友找Mirai酱要涩图/] --> checkperm
checkperm{检查插件开关} -- 关 -->reject
reject[拒绝]
checkperm -- 开 --> checkcd
checkcd{检查冷却时间} -- 到了 --> getsetudata
checkcd -- 没到 --> reject
getsetudata[获得涩图数据] --> download[下载涩图本体] --> checktag
checktag{检查涩图标签\n（R18之类的）} -- 能发 --> sendwithimg[发送图片] --> withdraw
sendwithimg --> checkassets
checktag -- 不能发 --> sendwithoutimg[仅发送文字描述] --> checkassets
withdraw[等待1分钟后撤回涩图]

cache[(涩图缓存)] -.-> getsetudata
checkassets{检查涩图缓存} -- 不足 --> 获取一包新的涩图 -.-> cache
checkassets -- 充足 --> over[什么都不做]
checkassets -.- cache

init[/插件初始化/] --> loadcache
loadcache[从文件加载保存的缓存] --> checkassets
loadcache -.-> cache

</pre> 

（尝试用 mermaid 做了流程图，但是一坨） 

- 管理员可以无视插件开关要涩图  
- 涩图下载失败也会发送提示
- 涩图发送失败会改为仅发送文字描述  
- 涩图发送成功后会在两分钟后撤回
- 程序退出时会保存缓存到文件

### 图源

来自 [Lolicon API](https://api.lolicon.app/)，非常感谢它的开发者

## 明日方舟抽卡模拟

插件名：ArknightsGacha

### 数据获取

干员列表来自 [BiliGame Wiki](https://wiki.biligame.com/arknights/%E5%B9%B2%E5%91%98%E6%95%B0%E6%8D%AE%E8%A1%A8)  
把获取的网页用 xpath 一翻，数据就来了

> 什么？为什么不是 PRTS ？

~~因为 B 站他直接把数据丢网页源代码里了，大好人属于是~~

根据干员的获取途径生成中坚和常驻两个池子  
~~限定池子理论上也能做，但是群友急了于是先这样了~~

能够通过指令让 Mirai 酱立即更新干员数据

### 运行逻辑

插件初始化时会从本地加载保存的干员数据  
加载完毕后生成两个字典（代表中坚和标准两个池子），键为星级，值为对应的干员列表  

每次请求抽卡时定义一个初始概率表：
```python
{
    3: 0.4,
    4: 0.5,
    5: 0.08,
    6: 0.02,
}
```
很明显 键是星级，值是概率  

抽卡会传入一个`combo`值，代表连续抽了多少抽没出 6 星。这个值可以经由群员的QQ号查到，用以实现 “若连续 50 发未出 6 星，下次 6 星概率增加 2%，直到第 100 发时必出 6 星” 的设定  
抽卡函数会根据上述逻辑对初始概率表进行修改，然后通过带权重的随机来确定抽到的星级，最后用初始化时生成的字典来得到具体抽到的干员数据，返回给负责交互的部分

`combo`值由负责交互的那一坨保管，每当抽到 6 星时这个值就重设为`0`
在收到干员数据之后负责交互的那部分便生成文本，回复给群友

## B 站动态转发

插件名：BiliDynamicForwarder

需要配合`BiliLogin`插件食用

### 运行逻辑

每 5 分钟请求一遍监听对象们的历史动态，与上一次请求的数据比对，找出新发布的进行发送  
~~↑ emm我的“找出新发布的动态”这个部分似乎还有点问题，时不时就会把历史的某一条动态刨出来发了~~

如果动态内容命中了黑关键词，那么它将不会被发送；  
但是如果同时又命中了白关键词，那么它还是会被发送。

这个插件的交互部分主要就是设置监听对象，逻辑还挺简单的，难写的原因是没有现成的鉴权和指令系统  
其余部分都是主动发送了没什么好说的  

自动维护一个用 B 站 uid 查昵称的字典，用来方便添加移除监听对象

### 关于接口

来自易姐的 [`bilibili-API-collect`](https://socialsisteryi.github.io/bilibili-API-collect/) ，非常感谢 ta  
函数封装由我实现，弄得一坨

~~emm注意到动态获取接口已经有了新的、更优雅的替代方案，但是我懒~~

## 萌属性抽取

插件名：MoeAttriLottery

之前写好这个插件的时候还有些沾沾自喜，然后看了别人家的机器人才发现，这其实是几年前就玩过时了的东西w  
~~果然是你能想得到的东西，别人早就想到了~~

### 数据源

由我整理自萌娘百科-[萌属性](https://zh.moegirl.org.cn/%E8%90%8C%E5%B1%9E%E6%80%A7)

### 运行逻辑

非常简单的带权重的随机抽取而已，一天仅能抽一次

拥有权重机制，比如我能把年龄属性中的“萝莉”调到`10`，其他全为`1`😋  

拥有主副属性机制，当抽到的主属性在副属性中有键时，会再抽一个副属性出来  
比如抽到了渐变色瞳，还可以再抽到一个红->蓝渐变的细分支  

## B站登录

插件名：BiliLogin

因为最近B站对获取动态的接口加了风控，非登录情况下的请求难度大幅提升，于是加紧写了这个插件用于登录

登录后cookies会自动留在bot框架中，B站动态转发插件可以直接使用

仅能通过管理员私聊进行扫码登录，不知道时间久了会不会出问题

---

emm 大概就是这样，以后应该还会继续维护 Mirai 酱的，有种养女儿的快感 😋

