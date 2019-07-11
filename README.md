# 富聊审核后台前端优化脚本概述

功能概述 
=======
1 常规文字进审提醒 回车提交   
2 截屏UI优化 键盘操作 数量统计 推流无弹窗 可选的双击换一批/右键推流/隐藏按钮/图片尺寸   
3 重点流UI优化 批量叉掉无弹窗 可选的双击换一批  
4 坐等UI优化 悬浮按钮 可选的双击换一批   
5 视频流 优化，使用video.js+hls.js替代flash  
6 小视频 优化，审核和复查页面可以打开悬浮可拖动可调速的视频播放容器 

主要功能
========

视频聊坐等/直播间截屏UI优化，添加了一些鼠标键盘操作方法
------

常规和文字进审提醒
------

敏感词秒杀已审核标记（已废弃）
------

直播视频流优化
------

小视频审核/复查 优化
------
借助工具
=========
Tampermonkey ，俗称“油猴子”。
--------
开发背景
=======
   自十月份以来，我们对直播的监管方式由以前的视频流审核变为了直播截屏审核。在各位同事的共同努力下，大家都慢慢习惯适应了直播截屏审核。直播截屏给人的感觉不像之前视频流那样笨重。同时结合截屏的特殊性，就需要我们及时的尽快看完当前审核页面的内容并换一批，所以直播截屏审核页面沿用直播视频流页面的布局就使得截屏审核效率提升的可能变小。因此我把个人在审核工作中对提高审核效率的想法法结合同事们的意见，对各个审核模块进行优化。

优化内容
========

   直播间截屏不同于视频流，我们不需要刻意花时间去盯着每个直播间观察行为，只需要判断当前直播间截图的状态。因此我们不仅需要大小合适的图片尺寸，也需要页面布局对图片的尽可能展示。因此，我去除了图片周围及外部边框的大量空白，使图片看起来十分紧凑。同时也去除了部分按钮和简短了部分用户信息 （早期的截屏审核中有拉黑和推荐按钮，才艺，PK中，发起方，接收方等），使用户信息和处罚按钮部分也变得十分紧凑和整齐。使得当前批次的图片尽量都在网页的首屏展示，减少鼠标滚轮操作（长时间聚焦滚动的图片会造成视觉疲惫）。以上是对审核页面UI的处理（其他审核页面关于UI的处理也都类似如此）。此外，功能方面增加了处罚键盘快捷键，可选的双击换一批、隐藏显示所有按钮、设置审核图片尺寸等。审核效率方面取消了点击处罚按钮后弹出操作面板的动画并使其出现在当前正在操作用户周围，在页面下方增加了当前页面停留时间起到督促作用。

   在常规和文字等的审核中，最不方便的就是审核模块众多，新后台出现后有所好转。但是在审核时仍然需要我们经常定时轮流点击刷新这些模块查看是否有新内容进审。不仅浪费效率，还有可能漏审或超时。因此，常规部分（不含新后台）、文字类、直播封面可在页面右上角的定时刷新器设置刷新时长，页面会在指定时间到达后刷新页面，如果有新内容进审，则网页的title会闪烁提醒。审核时自动刷新停止，提交后若无新内容进审则自动刷新继续运行，否则继续闪烁提醒。

   flash插件过于笨重且已不被主流浏览器兼容， 部门内部分电脑近期也经常性的出现flash崩溃的情况。 使用 video.js + videojs-contrib-hls.js结合video标签代替flash作为直播视频流播放容器。同时解决了直播PK的展示尺寸问题。

优化结果
========
直播间截屏审核页面每批次首屏可展示3行图片（隐藏按钮后为4行），每行可展示图片14张，最大可展示56张，比优化前多24张。

视频聊坐等收费方每批次数量为26个，比优化前多8个。  

从根本上解决了直播视频流 播放过程中flash崩溃的问题。  

小视频审核复查时可以在当前页面打开，并可以调速拖动。  

审核部 尹成诺
========
2018.12.24
========

# 审核后台直播流优化方案

现状分析： 
=======
   目前审核后台的直播播放使用的是传统的Flash播放RTMP协议的视频流。但是随着浏览器技术发展，越来越多的浏览器开始放弃对Flash的支持。以新后台为例，目前直播监管只能在最新版的360安全浏览器上播放，而且在播放流数量较多时，页面交互时浏览器反应会很慢，给人笨重的感觉。而其余浏览器不是不支持，就是在播放过程中出现频繁立即修复浮层。目前的情况对于直播监管来说，是存在一定风险的。因此对于直播流播放，需要一种通用的解决方案，即可以在多数浏览器运行，同时提升页面流畅度。 

优化概述：
=======
   使用video+ flv.js播放视频流。flv.js 是一个实现了在video中播放FLV格式视频的js库，由哔哩哔哩开源。它的工作原理是将FLV文件流转码复用成ISOBMFF（MP4碎片）片段，然后通过 Media Source Extensions将MP4片段喂进浏览器。拉流使用HTTP-FLV协议，使用类似RTMP流式的 HTTP 长连接，兼顾RTMP和HLS的优点。唯一需要注意的是因为是用的HTTP长链接，拉流时会受到浏览器对于同一域名请求的最大并发连接数限制，一般是6个。因此同一流地址需要准备多个域名，在实例化flv对象之前要对视频流地址进行域名分发。

效果对比：  
========
1 浏览器兼容方面：  
------
   优化前：仅在360安全浏览器等浏览器上运行。  
   优化后：可以在实现Media Source Extensions的任意浏览器上运行（约80%的浏览器都已实现）。  
2 页面响应速度方面：  
------
   PS：受电脑性能、浏览器类型等因素影响，不同环境下得到的测试结果可能不同。下面的结果符合审核部门多数电脑的表现情况。参考数据为直播审核页面的当前页面停留时间，以下数据是多数情况下的肉眼观测结果，严格来说并不精准，仅做参考。因为受Tampermonkey运行机制的影响，优化后的响应时间比理论上的响应时间要慢2s左右。  
   优化前：直播流数量为20个时，从点击提交到所有直播流画面展示出来需要6s左右；直播流数量为40个（即双屏各开20个）时，需要8s左右。且一边屏幕的20个提交时，另一边页面会对操作没有响应。  
   优化后：直播流数量为30个时，从点击提交所有直播流画面展示出来需要4s左右，且双屏互不影响。  
3 可扩展性方面：  
------
   优化后：使用video标签，简洁易用，无论样式还是功能都具有高度的可定制和扩展性。  


审核部 尹成诺
========
2018.07.05
========
