---
title: 尝试详细介绍一下我的 BiliTools
date: 2023-12-29 15:36:00
tags: [Bilibili, Python, Tkinter]
categories: [Program]
---

想要介绍一下陪了自己两年多的项目 ~~, 顺便练一下 markdown 语法~~

阅前提示: 
1. 以下内容的措辞可能令人费解, 因为咱在表达方面并没有什么天赋
2. 以下内容不一定正确或者符合规范, 如果有错误欢迎提出w

# Intro

BiliTools 是我在 2021 年 5 月左右使用 Python 编写的一个用于下载 B 站的各种资源的程序

[项目地址](https://github.com/NingmengLemon/BiliTools)

## 关于版本号
因为有前身在, 所以版本号直接从 2 开始
又因为咱的脑子可能有点抽风, 版本号目前都是 v.2.0.0_Dev{实际更新版本数} 这个样子w
目前更新到了 Dev17 的说, 以下内容都是适用于该版本的

## 关于前身
前身是由我编写的 BiliDownloader, 其实就是一个 [you-get](https://github.com/soimort/you-get) 的 GUI 而已
附加了一点针对 B 站的功能, 比如弹幕下载和过滤, 版本号为 1

## 史山注意
首先最初在写这个程序的时候我还是个若至, 像各种语言规范什么的都不知道

你可以在程序中见到:
- 随处可见的超长单行
- 刁钻古怪的调用链
- (几乎)没有类型标注
- 稀少的注释与日志
- 一些不知道在干什么但是删掉就会出 Bug 的代码
- 各种若至的代码逻辑
- 各种花里胡哨但又一堆 Bug 的功能

然后到现在我稍微懂了那么一点语言规范, 但是先前拉的史已经不好收拾了

有的时候甚至都不想再继续写, 想 remake 一个新程序出来了

这个程序伴随着我的进步, 我的程序水平也在逐渐提升（吗？）

~~所以还是蛮有纪念意义的, 对吧~~

## 感谢

[Bilibili-API-collect](https://github.com/SocialSisterYi/bilibili-API-collect)

[danmaku2ass](https://github.com/m13253/danmaku2ass)

# Quick Overview

## 文件结构

截至 Dev17 的文件结构如下
```
BiliTools
├─ bilitools.py             # 主程序
├─ basic_window.py          # 提供了(几乎)所有窗口的基类
├─ bezier_curve.py          # (未使用)用于画贝塞尔曲线的一些基本操作
├─ configuration.py         # 一些与配置相关的数据与操作
├─ custom_widgets.py        # 一些自定义的 tk 组件
├─ danmaku2ass.py           # 修改过的 danmaku2ass
├─ ffdriver.py              # 用于调用 ffmpeg 的操作
├─ imglib.py                # 图标们 (以 base64 方式编码的 png)
├─ textlib.py               # Tips 和 About 文本
├─ videoshot_handler.py     # 提供了用于处理视频快照的一个类
└─ biliapis                 # 封装了一些 B 站的 API, 大多取自 bilibili-api-collect
     ├─ __init__.py         # 用于统领起这个 API 库, 定义了一些杂七杂八的操作
     ├─ requester.py        # 用于请求的模块
     ├─ error.py            # 定义了错误类
     ├─ bilicodes.py        # 各种常用的标识码
     ├─ wbi.py              # WBI 鉴权
     ├─ article.py          # 专栏相关
     ├─ audio.py            # 音频相关
     ├─ comment.py          # 评论相关
     ├─ danmaku.py          # 弹幕相关
     ├─ dynamic.py          # 动态相关
     ├─ live.py             # 直播相关
     ├─ login.py            # 登录相关
     ├─ manga.py            # 漫画相关
     ├─ media.py            # 影视与番剧相关
     ├─ stream.py           # 媒体流相关
     ├─ subtitle.py         # 字幕相关
     ├─ user.py             # 用户相关
     ├─ video.py            # 视频相关
     └─ other.py            # 其他 API
```

## 依赖
```
tkinter         # GUI 库, Python 应该会自带
pillow          # 用于图像处理
qrcode          # 用于生成二维码
beautifulsoup4  # 用于处理 HTML 和 XML
brotli          # 用于解压数据
lxml            # bs4 的依赖
colorama        # 用于控制台着色
pycryptodome    # 用于计算 CorrespondPath
```
[ffmpeg](https://github.com/FFmpeg/FFmpeg) - 通过管道进行调用, 安装即可

# Go ♂ Deeper

## 各个类间的继承关系

<details><summary>点击展开</summary>

```
object
├─ DownloadManager
├─ Window
│   ├─ MainWindow
│   ├─ BatchWindow
│   ├─ InputWindow
│   ├─ ConfigWindow
│   ├─ AudioWindow
│   ├─ CommonVideoWindow
│   ├─ CollectWindow
│   ├─ LoginWindow
│   ├─ PbpShower
│   ├─ PartsChooser
│   ├─ BlackroomWindow
│   ├─ BangumiWindow
│   ├─ MangaViewer_Rolling      (未完工)
│   ├─ MangaViewer_PageTurning  (未完工)
│   ├─ SearchWindow
│   ├─ PlotShower
│   ├─ VideoShotViewer
│   ├─ ArticleWindow    (未完工)
│   ├─ ToviewWindow     (未完工)
│   └─ Thread_with_gui
├─ ToolTip
├─ _CustomMsgbox
└─ VideoShotHandler

tk.Frame
└─ VerticalScrolledFrame
    ├─ _CommonVideoSearchShower
    └─ _MediaSearchShower

ttk.Button
└─ ImageButton

tk.Label
└─ ImageLabel

tk.Toplevel
└─ _TipWindow

Exception
└─ BiliError

threading.Thread
└─ _DownloadThread

```

</details>

## 多线程的处理

就像其他基于 tk 的应用程序一样, 此程序也以主线程为主

为了处理那些耗时的任务, 自然是需要用到子线程的, 但是子线程没办法直接修改 tk 窗口中的对象, 否则就会导致`thread not in mainloop`错误

于是我在`basic_window.py`中的`Window`基类中定义了一个`task_queue`成员. 这是一个队列, 子线程可以向其中放入无参的函数对象, 而这些函数对象会被位于主线程中的由`Tk.after()`驱动的一个自动循环执行的函数`listen_task`自动取出并在主线程中执行. 此时这个函数是在主线程中被执行的, 就可以直接修改窗口中的对象了.

像这样, 耗时的任务就可以在子线程中执行完毕, 然后把数据装在上述的函数中发送给主线程, 再由这个函数填充到窗口中 (填充的过程其实耗时很少)

<details><summary> 这个基类的多线程控制部分</summary>

```python
class Window(object): # 程序中所有常规窗口的父类
    def __init__(self, ..., toplevel=False, ..., master=None):
        self.task_queue = queue.Queue() # 此队列用于储存来自子线程的无参函数对象
        if toplevel or master:
            self.window = tk.Toplevel(master=master)
        else:
            self.window = tk.Tk()
        ...
        self.listen_task()

    def listen_task(self):
        if not self.task_queue.empty():
            func = self.task_queue.get_nowait()
            try:
                func()
            except Exception as e:
                logging.error('Task Listener Caught an Error: '+str(e))
                #raise
                traceback_info = traceback.format_exc()
                # print(traceback_info)
            else:
                pass
                #logging.debug("Call func: "+str(func))
        if self.task_queue.empty():
            self.window.after(10,self.listen_task)
        else:
            self.window.after(1,self.listen_task)

    ...
```
> `...`是被省略的部分
</details>

我通过这种方式来进行多线程协调.

~~听上去还是蛮巧妙的?~~

但是, 把这个方法写在基类中就意味着, 每个窗口创建时都会附带一个队列和一个反复执行的函数. 我不清楚别人是不是这么做的, 反正我当时就这么写了w.
现在我对这个做法其实不是很满意, 因为我认为这可能消耗了额外的资源 ~~, 多少有些强迫症了~~

## 网络请求的封装

比较让人难绷的是, 我以前把负责网络请求的模块`requester`直接放在了`biliapis`模块中, 我现在觉得这个层次结构不算合理.
这也就是说, 要想把`biliapis`模块用到别的项目中, 底下的`requester`也会被 copy 一份, 而那个别的项目中可能已经有了别的网络请求模块, 就会造成冗余...
然后`requester`中封装的`get`与`post`还对返回的流对象做了处理, 把请求过程中创建的`req.Request`对象嗯塞了进去, 这是为了方便`login`模块中对 cookies 的操作. 但也进一步导致了可移植性的下降.

<details><summary>搓程序时候的趣事(?)</summary>

ChatGPT 一开始说用`urllib`打开的流中会有`request`这个成员, 但是其实使用 opener 打开的流中并没有, 当时跟 GPT 杠了挺久, 然后 GPT 改口了ww

GPT 最终的答复是:

> `opener.open()`方法返回的`HTTPResponse`对象可能不会附带`request`属性。这是因为`opener.open()`方法是一个高级接口，它隐藏了底层的请求细节，不直接暴露`Request`对象。

</details>

以`get`方法为例, 它的代码如下
```python
@auto_retry(retry_time)
def get(url, headers=fake_headers_get):
    req = request.Request(url, headers=headers)
    response = opener.open(req, None, timeout=timeout)
    response.request = req      # 强行塞入 request
    logging.debug('Get: '+url)
    return response
```
<details><summary>我猜你想吐槽...</summary>

> 啊你这个请求怎么没有用`with`啊
> 这个函数怎么直接就把流对象传回去了啊

emm来看其他部分是怎么用这个函数的:
```python
def get_content_bytes(url, headers=fake_headers_get, update_cookie=True):
    # 取名有问题, 请自动忽略 content 这个词
    with get(url, headers=headers) as response:
        if cookies and update_cookie:
            cookies.make_cookies(response, response.request)
        return read_and_decode_data(response)
        # 取名也有问题, decode 应该改成 decompress
```
~~我其实不是很确定用自动重试装饰器来修饰这个函数是不是正确的选择~~

</details>


总之就是很不满意 (叉腰)

## 多窗口之间的协作

首先需要提醒的是, 下文提到的`Window`(定义在`basic_window.py`中)继承自`object`, `tk.Tk`或者`tk.Toplevel`对象则被存放在`Window.window`处

~~很令人费解是吧? 我也想问问以前的我为什么要这么写~~


我一般会在窗口类(一般继承自`Window`)的`__init__()`方法的末尾加上`self.window.wait_window(self.window)`来使外界在执行实例化窗口类的操作时停在这一步, 直到窗口被释放 (`self.window.destroy()`被执行)
官方的模块 `tk.messagebox` 和 `tk.filedialog` 中的操作也有类似的逻辑

> 当时的我在`Window`类中直接把上面那个"停下"的操作封装成了`self.mainloop()`这个方法, 极具迷惑性.
> 注意与下文提到的`mainloop`做区别.
> ~~越写就越想穿越回去抽自己几个大嘴巴子~~

对于大部分不需要在打开窗口的同时打开另一个窗口的窗口是这样.
至于为什么不用`Tk.mainloop()`来等待窗口, 是因为我发现如果这样做的话, 关闭窗口时会产生一些莫名其妙的问题
按照我的理解, `Tk.mainloop()`会使整个 tk 的事件循环阻塞主线程; 如果通过`Tk.destroy()`将其打破, tk 会认为整个程序应该结束了, 但其实没有, 这只是一个窗口关闭了而已.

于是我的整个程序里一个`Tk.mainloop()`都没用过w

而除了下载窗口外的所有子窗口(`Toplevel`)的父窗口都是主窗口(`Tk`), 当把主窗口关掉的时候其余窗口就会被自动地关闭.
至于下载窗口, 它拥有特殊的内部逻辑, 并没有使用等待窗口这一机制.

## 下载器的内部逻辑

这一部分最初于 2021 年 11 月左右完成 (根据写的日志来看)
~~当时这部分是在自习课上想出来的, 笔记本应该还能找到, 但是放在家里了w~~

**这一部分是线程安全问题的重灾区.**
中途曾尝试修复过一次, 给涉及到多线程操作的几个列表添加了线程锁. 但是偏偏漏掉了最核心的用于存储原始数据的列表和另外三个用于存放任务状态的列表. 
我最终发现这个遗留问题的时候已经为时已晚. 各个线程锁在之前尝试修复的时候搅成了一坨, 就连继续往上拉史都做不到了, 哪怕只加一个线程锁都会在莫名其妙的地方锁死.
~~当然如果花时间的话其实还是可以修的, 需要把之前修的地方推掉重来~~

### 显示机制

并不是通过修改窗口属性将构建好的窗口显示或隐藏, 而是到了需要显示的时候才开始构建

当 用于接受下载任务的`task_receiver()`被调用 且 这个函数运行于主线程中 时, `show()`方法会被自动调用
`show()`方法会执行构建 GUI 的代码, 而`hide()`方法则会直接销毁整个窗口
由于涉及到 GUI 操作, 上述两个方法都必须在主线程中运行

窗口中显示的的任务信息存储在`table_display_list`这个多维列表中, 每次刷新显示的信息时`auto_refresh_table`这个方法会将它的内容展示到 GUI 中

> `auto_refresh_table`也是借助`Tk.after()`来进行自动循环的

下载器刷新 GUI 的流程:

1. 刷新任务列表
   - 如果任务数没有变动, 那么直接修改对应项的值
   - 如果任务数有变动:
       1. 先记录哪些项被选中了
       2. 然后删除列表框中的所有项
       3. 将新的信息填充进去
       4. 复现先前选中的项
2. 更新统计信息
3. 准备下一次自动刷新

### 数据存储与任务管理

最核心的内容是`data_objs`这个成员, 它存储着传入`task_receiver`的原始数据
下载的各个步骤都会读取

有三个索引列表与它相关
- `failed_indexes`
- `running_indexes`
- `done_indexes`

存放的内容是任务对应的数据在`data_objs`中的索引
<small>也是在`table_display_list`中的索引</small>

> 我认为下载器中的最关键的一个量就是这个索引
> 在特化的任务函数中第一个显式参数就是`index`, 说的就是这个索引

感觉逼逼叨叨这么多还不如放张图, 等我...

<details><summary>试着用了 mermaid 语法</summary>

<pre class="mermaid">
flowchart TB

    F(下载管理器实例化) -- 启动 --> auto_thread_starter
    
    subgraph auto_thread_starter
        direction TB
    
        b{检查 task_queue}
        b -- 有任务 --> c[将任务取出, 作为子线程开始运行]
        b -- 无任务 --> b
        c --> b
    end
    
    c -- 启动 --> task_itself
    
    subgraph task_itself
        direction TB
        g[将参数中的 index 放入 running_indexes]
        g --> h[任务计数器+1]
        h --> i[进行任务内容]
        i --> j[将 index 从 running_indexes 中移除]
        j --> k{任务是否成功}
        k -- 是 --> l[将 index 放入 done_indexes]
        k -- 否 --> m[将 index 放入 failed_indexes]
        l --> n(任务计数器-1)
        m --> n
    end

</pre>

</details>

如果要添加任务, 则遵循下面的流程:

<details><summary>另一个流程图</summary>

<pre class="mermaid">
flowchart TB
    direction TB
    A(添加任务) --> B[获取到下载所需的数据]
    B --> C[将任务数据放入 data_objs]
    C --> D[
        将对应的任务函数和任务在 data_objs 中的索引和数据包装成 lambda
        然后放入 task_queue
        ]
    D --> E[
        在 table_display_list 中创建位置
        <small>任务在两个列表中的索引保持一致</small>
        ]
</pre>

</details>

### 下载进度的保存与读取

#### 保存

程序会遍历整个`data_objs`, 从中筛选出还没有成功的任务组成一个列表
然后将这个列表作为一个临时字典的`objs`键对应的值
`table_display_list`中对应的项也会被组成列表, 作为这个临时字典的`displaylist`键对应的值
最后程序会将这个字典保存为 json 文件

上述操作被封装为了`save_progress`这个方法
这个方法在每次有任务开始或结束的时候都会被调用

#### 读取

首先程序会读取保存的 json 文件, 然后遍历其中的`objs`列表, 按照与`task_receiver`中类似的方法逐个添加任务
在这个过程中, 除了任务状态文本会被改成`待处理`外, 其余内容将会与保存前保持一致

上述操作被封装为了`load_progress`这个方法
这个方法在程序启动的时候经过用户确认后会被调用

## Tkinter 中的滚动页面的实现

我最开始定义了一个继承自`tk.Frame`的滚动框架`VerticalScrolledFrame`

在用它展示条漫时我发现它的最大滚动范围在 31000px 左右, 这可能是`canvas`的极限

层次结构是: 
frame > canvas > frame > 页面元素

<pre class="mermaid"> 

flowchart LR

subgraph top_frame
    subgraph canvas
        subgraph bottom_frame
            page_content
        end
    end
    scrollbar[
        S
        c
        r
        o
        l
        l
        b
        a
        r]
end

</pre>

emm大概长这样, 可能有些夸张了

缺点是需要手动给页面元素绑定鼠标滚动等事件
纵向滚动条用于控制页面的滚动, 实质上是在控制`canvas`中的里层`frame`的上下移动

这个框架被设计成宽度由内容物的宽度决定, 高度由用户定义
被用在了`SearchWindow`的`Notebook`的单个分页中

如果需要左右滚动的话也同理了
加上一些自动调用的代码应该就能实现像橱窗那样的循环展示效果 ~~(虽然我没亲自写过)~~

## 互动视频剧情图展示器的实现

整个剧情图的展示基于`tk.Canvas`画布, 在画布右侧和下方分别加上了纵向滚动条和横向滚动条

最核心的内容是`self.plots`与`self.explored_plot_ids`
`self.plots`是一个装着(装着字典的列表)的列表, 分层存储着请求到的原始数据
节点们组成类似于有向图的结构, 像下面这样
~~一开始我其实是把它当作树来处理的~~
<pre class="mermaid">
flowchart TB
    subgraph self.plots
        subgraph layer_1
            plot_1
        end
        subgraph layer_2
            plot_2
            plot_3
            plot_4
        end
        subgraph layer_3
            plot_5
            plot_6
        end
    end

    plot_1 --> plot_2
    plot_1 --> plot_3
    plot_1 --> plot_4
    
    plot_2 --> plot_5
    plot_2 --> plot_6
    
    plot_3 --> plot_5
    
    plot_4 --> plot_5
    
    plot_6 --> plot_2
</pre>
<small><del>我超这个`mermaid`的牛魔绘图啊</del></small>
分层是为了更有层次地绘图

各个节点之间的连接由每个节点数据中包含的多个`jump_edge_id`确定

为了方便画图, 引入了`self.explored_plot_ids`, `self.plot_coors`
`self.explored_plot_ids`是一个字典, 以`节点id: (节点所在的层的索引, 节点在层中的索引)`的形式存储节点id到节点数据的映射
`self.plot_coors`是一个字典, 以`节点id: (节点在画布中的x坐标, y坐标, 节点块的宽, 节点块的高)`的形式存储节点id到节点块在画布中的位置的映射

为了方便下载, 引入了`self.cid_map`和`self.avid_map`这两个字典
它们分别存储节点id到cid的映射和cid到avid的映射

### 节点的探索

互动视频有两个重要的id, 剧情图id`graph_id` 和 节点id`edge_id`
剧情图id代表这个互动视频, 节点id代表它的节点
> 剧情图id可以通过向[这个接口](api.bilibili.com/x/player/v2)提交互动视频的`cid`和(`avid`或`bvid`)得到

> 每个节点id都有对应的`cid`, 配合互动视频的`bvid`或`avid`可以取到视频的流
> 初始节点的`cid`就是这个互动视频的`cid`
> <small>其实互动视频的节点就是另一种形式的视频分P</small>

> 对接口的节点id参数传入`0`就可以获得初始节点的数据

借由初始节点的数据, 可以得到它可能跳转到的节点们的id
再请求这些节点id的数据, 获得它们可能跳转到的节点们的id
就这么一直下去, 直到不再有新的节点id出现
> 如果一个节点没有跳转到的节点, 就说明它是终止节点

将每一轮上述操作获得的数据按轮分层 <small>(初始节点单独一层)</small>, 就完成了节点探索的操作

### 节点的绘制

<details><summary>先看效果()</summary>

目标视频: [电棍家中的惨案](https://www.bilibili.com/video/BV1Pt4y1S7Wb/)

![Plot_Shower](/assets/blogs/BiliTools-introduction/plot_shower.png)

起始节点被涂成淡红色, 终止节点被涂成淡绿色

</details>

遍历整个`self.plots`, 为每个节点绘制块, 然后为每个节点绘制跳转箭头
- 如果节点跳转到的是下一层的节点, 那么直接连接即可 <small><del>原本想用贝塞尔曲线来连接的, 然后寄了</del></small>
- 否则则将箭头拉到画布顶部或底部, 绕到目标块所在层的后面进行连接(跳连)

设置了一些偏移值来确保箭头的线不会重合.
<small>(但是对于一些关系复杂的互动视频, 上述策略会造成跳连的箭头太多, 导致绘图非常混乱, 完全没法读)</small>

<details><summary>翻车了</summary>

目标视频: [收养澄闪](https://www.bilibili.com/video/BV1eq4y1e7QF/)

![Plot_Shower_failed](/assets/blogs/BiliTools-introduction/plot_shower_failed.png)

~~视频做得太好导致的~~

</details>

以后可能会做一个生成`mermaid`流程图的功能? ~~(画饼ing)~~

总之这玩意真就图一乐

### 交互

#### 点击

我想实现`当点击一个节点块的时候, 打开侧边栏并在其中展示这个节点的信息`这个交互逻辑

首先来实现点击绘图对象罢.
<small>`tk.Canvas`其实是可以为其中的绘图对象绑定点击事件的, 但我当时不知道w</small>
我给整个 canvas 绑定了一个点击事件, 根据坐标来判断被点击的节点块

所谓的侧边栏, 是另一个隐藏在右边的框架
当它要显示的时候, canvas 的宽度会减小侧边栏的宽度那么多, 然后侧边栏框架出现

值得注意的是, 我已经发现了超出 canvas 范围的的强制滚动会使我的点击事件错位这个问题, 我会在后续将它修复

~~简称: 下次一定~~

#### 拖动

<small>写到这里的时候发现我之前写的拖动操作寄了</small>

~~那么今天的内容就到这里了让我们下次再见~~

# Defects

## 线程安全问题

这个问题在 [下载器的内部逻辑](#下载器的内部逻辑) 这部分有提到

大概就是, 如果有多个线程*同时*操作一个*线程不安全*的对象, 就可能会出问题
Python 中的字典是线程安全的, 但是列表不是

为了避免这个问题, 可以:
- 给操作对象加上线程锁, 使同一时间只能有一个线程访问它
- 换用线程安全的对象来存储信息

上文提到的`修复`, 便是给对象加锁
~~结果加成了一坨 (恼)~~

## 默认参数评估问题

这个问题潜伏了很久, 直到在我使用`custom_widgets`中的`run_with_gui`这个函数的时候才被发现

<details><summary>发现的过程</summary>

程序第一次调用`run_with_gui`时, `is_progress_hook_available`这个参数被传入了`True`.
这会使这个函数传入的`func`参数(这是一个函数)在后续的过程中被*额外传入*一个叫做`progress_hook`的字典, `func`函数可以通过修改这个字典来汇报任务的进度

问题就在于额外传入这个操作上.
在老版本的程序中, 程序在准备阶段会直接修改先前传入的`kwargs`, 以便后续方便地使用`func(**kwargs)`来传入参数.
而我将`kwargs`的默认参数设定为了`{}`, 而不是`None`.

第二次调用的时候到了, 这次`is_progress_hook_available`这个参数被传入了默认的`False`(这倒不是问题的所在), 按照程序的设计, `kwargs`不会被修改. 比较巧合的是, 第一次传入的函数只有`progress_hook`这一个参数, 而第二次的函数则是无参的, 两次调用的`kwargs`这个参数都没有被指定.
这就导致了后文所说的默认参数评估的问题.

> <font color=red face=consolas>TypeError: self.func() got an unexpected keyword argument 'progress_hook'</font>

~~解释器: Aughhhhhhhhh~~

</details>

虽然我自主地修复了这个问题, 并且知道它导致的问题是前一次调用影响到了后一次, 但以我的水平无法解释它的成因

有请: GPT 先生!
> 在Python中，函数的默认参数如果设置为字典（或其他可变数据类型，如列表、集合等），会有一个潜在的陷阱：如果默认参数在函数内部被修改了，那么这个修改会在未来的函数调用中持续存在。这是因为默认参数只会在函数定义时被评估一次，不是每次调用时都评估。这意味着，如果你修改了默认参数的内容，那么下一次调用函数时，默认参数的值不再是定义函数时指定的原始值。
> 这个行为对于不可变的默认值（例如None、整数、字符串等）通常不会导致问题，因为它们不会被函数调用所改变。然而，对于可变的默认值（例如字典、列表等），这种行为可能导致意外的副作用，因为如果函数内部修改了这些可变对象，这些修改会影响到下一次函数调用时这些默认参数的状态。

<details><summary>ChatGPT 举的例子</summary>

以一个列表作为默认参数的函数为例，来展示这个评估过程：

```python
def append_to_list(value, my_list=[]):  # 默认参数在这里被评估一次
    my_list.append(value)
    return my_list
```

在这个例子中，`my_list=[]` 是一个默认参数。这个默认参数的列表对象在函数定义时创建，并且只创建一次。如果你在函数内部修改了 `my_list`，比如通过调用 `append_to_list` 函数，那么你实际上是在修改这个单一的、在函数定义时创建的列表对象。这样，下一次调用 `append_to_list` 时，你将看到上一次调用的结果仍然保留在列表中，因为它是同一个列表对象。

为了避免这种情况，你应该使用一个不可变的默认值，比如 `None`，然后在函数内部检查它，并在需要时创建一个新的列表：

```python
def append_to_list(value, my_list=None):
    if my_list is None:
        my_list = []  # 在这里为每次调用创建一个新的列表
    my_list.append(value)
    return my_list
```

在这个修正后的版本中，每次调用 `append_to_list` 时，如果没有提供 `my_list` 参数，就会创建一个新的空列表。这样，每次函数调用都是独立的，不会有意外的共享状态。

</details>

BiliTools 中的很多函数都被我像这样写了, 比如这个:

```python
def _video_download_thread(self, ..., subtitle_regulation=config['download']['video']['subtitle_lang_regulation'], ...):
    ...
```

这些问题目前还没有得到修复...
~~绝对不是因为我忘了! 我回头就去修!~~

## 其他问题

> 未完待续...