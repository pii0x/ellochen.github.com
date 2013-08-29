---

layout: post
categories: [Game]
tags: [Cocos2d-x 优化 翻译]

---

# 怎样优化内存使用  
    - 内存优化原则  
      + 了解瓶颈所在, 把它们显示出来~.  
      + 拒绝过度优化.  
    - 内存优化的层次  
      + cocos2d-x 客户端层  
      + cocos2d-x 引擎层  
      + c++ 语言层次  
    - 提示 & 技巧  
    - 推荐阅读  

## 内存优化原则  
为了优化程序的内存使用, 你应该知道是什么消耗了你的程序中大部分的内存. 答案就是*纹理*!  
纹理几乎占据了游戏中90%的内存. 所以我们应该尽最大的努力缩减我们程序中纹理的内存使用, 
否则当系统处于低内存警告时就会杀掉你的程序.  
这里我讲给出关于cocos2d-x游戏内存优化原则的两个一般性指导.  

### 了解瓶颈所在, 把它们显示出来  
哪种类型的纹理占据了大部分的程序内存? 这些纹理占据了多少内存?  
你可不要数着手指头胡乱猜测.  
恰好有这样一个工具集为你而准备的. 它们是苹果公司出品的工具, Allocations和Leaks.  
你可以在Xcode中长按Run键然后选择Profile选项来选择这两个工具.  
下面是截图:  
![](https://raw.github.com/ellochen/Img-store/master/instruments.jpeg)  
你可以使用Allocation工具来监控你的程序内存使用情况, 并使用Leaks来观察内存泄露.  
你仍然可以使用一些代码来获取关于程序内存使用的有用信息.  
给出代码:  

        CCTextureCache::sharedTextureCache()->dumpCachedTextureInfo();  

当你调用这行代码并在DEBUG运行你的游戏时, 你将在Xcode的Console窗口看到一些格式化的log.  
它们就像这样:  

        Cocos2d: cocos2d: "cc_fps_images" rc=5 id=3 256 x 32 @ 16 bpp => 16 KB
        Cocos2d: cocos2d: "XXX/hd/actor.pvr.ccz" rc=1059 id=4 2048 x 2048 @ 32 bpp => 16384 KB
        Cocos2d: cocos2d: CCTextureCache dumpDebugInfo: 2 textures, for 16400 KB (16.02 MB)
        
就像你看到的, 它显示了纹理的名字, 引用计数, id, size以及每像素有多少位. 另外非常重要的一点,   
它显示了纹理的内存使用情况.在这里*cc_fps_images*占据16KB的内存, 而actor.pvr.ccz却占据了16M内存之多.  

### 拒绝过度优化  
这是一个一般性的优化原则. 你在做内存优化的时候需要做适当的权衡. 因为有时图片质量和图片内存是对立的.  
永远不要去过度优化!  (译注: 作者这么激动,我猜他是想说否则美术MM会发怒的!)  
