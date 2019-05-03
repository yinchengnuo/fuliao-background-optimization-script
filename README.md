# 富聊审核后台前端优化脚本概述

功能概述 
=======
1 常规文字进审提醒 回车提交   
2 截屏UI优化 键盘操作 数量统计 推流无弹窗 可选的双击换一批/右键推流/隐藏按钮/图片尺寸   
3 重点流UI优化 批量叉掉无弹窗 可选的双击换一批  
4 坐等UI优化 悬浮按钮 可选的双击换一批   
5 视频流 优化，使用video.js+hls.js替代flash  

#主要功能
========

#视频聊坐等/直播间截屏UI优化，添加了一些鼠标键盘操作方法
------

#常规和文字进审提醒
------

#敏感词秒杀已审核标记（已废弃）
------

#直播视频流优化
------
#借助工具
=========
#Tampermonkey ，俗称“油猴子”。
--------
#开发背景
=======
#自十月份以来，我们对直播的监管方式由以前的视频流审核变为了直播截屏审核。在各位同事的共同努力下，大家都慢慢习惯适应了直播截屏审核。直播截屏给人的感觉不像之前视频流那样笨重。同时结合截屏的特殊性，就需要我们及时的尽快看完当前审核页面的内容并换一批，所以直播截屏审核页面沿用直播视频流页面的布局就使得截屏审核效率提升的可能变小。因此我把个人在审核工作中对提高审核效率的想法法结合同事们的意见，对各个审核模块进行优化。

#优化内容
========

#直播间截屏不同于视频流，我们不需要刻意花时间去盯着每个直播间观察行为，只需要判断当前直播间截图的状态。因此我们不仅需要大小合适的图片尺寸，也需要页面布局对图片的尽可能展示。因此，我去除了图片周围及外部边框的大量空白，使图片看起来十分紧凑。同时也去除了部分按钮和简短了部分用户信息 （早期的截屏审核中有拉黑和推荐按钮，才艺，PK中，发起方，接收方等），使用户信息和处罚按钮部分也变得十分紧凑和整齐。使得当前批次的图片尽量都在网页的首屏展示，减少鼠标滚轮操作（长时间聚焦滚动的图片会造成视觉疲惫）。以上是对审核页面UI的处理（其他审核页面关于UI的处理也都类似如此）。此外，功能方面增加了处罚键盘快捷键，可选的双击换一批、隐藏显示所有按钮、设置审核图片尺寸等。审核效率方面取消了点击处罚按钮后弹出操作面板的动画并使其出现在当前正在操作用户周围，在页面下方增加了当前页面停留时间起到督促作用。

#在常规和文字等的审核中，最不方便的就是审核模块众多，新后台出现后有所好转。但是在审核时仍然需要我们经常定时轮流点击刷新这些模块查看是否有新内容进审。不仅浪费效率，还有可能漏审或超时。因此，常规部分（不含新后台）、文字类、直播封面可在页面右上角的定时刷新器设置刷新时长，页面会在指定时间到达后刷新页面，如果有新内容进审，则网页的title会闪烁提醒。审核时自动刷新停止，提交后若无新内容进审则自动刷新继续运行，否则继续闪烁提醒。

#flash插件过于笨重且已不被主流浏览器兼容， 部门内部分电脑近期也经常性的出现flash崩溃的情况。 使用 video.js + videojs-contrib-hls.js结合video标签代替flash作为直播视频流播放容器。同时解决了直播PK的展示尺寸问题。

#优化结果
========
#直播间截屏审核页面每批次首屏可展示3行图片（隐藏按钮后为4行），每行可展示图片14张，最大可展示56张，比优化前多24张。

#视频聊坐等收费方每批次数量为26个，比优化前多8个。  

#从根本上解决了直播视频流 播放过程中flash崩溃的问题。

#数据支撑
========


#审核部 尹成诺
========
#2018.12.24
========

