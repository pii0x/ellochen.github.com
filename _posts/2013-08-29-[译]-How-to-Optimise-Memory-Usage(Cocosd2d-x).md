---

layout: post
categories: [Game]
tags: [Cocos2d-x 优化 翻译]

---

[占坑]
## 怎样优化内存使用  
- 内存最优化原则  
  + 了解瓶颈所在, 把它画出来~.  
  + 拒绝过度优化.  
- 内存优化的层次  
  + cocos2d-x 客户端层  
  + cocos2d-x 引擎层  
  + c++ 语言层次  
- 提示 & 技巧  
- 推荐阅读  

## Memory Optimization Principle
In order to optimise your app's memory usage, you should know what consumes most of your app's memory. The answer is Textures!

It almost occupies 90% of app's memory. So we should try our best to minimize our app's texture memory usage, otherwise your app may be killed by the OS
duo to low memory warnings.

Here I'll give you two general guides about cocos2d-x game memory optimization principle.

Know the bottleneck, then figure it out.
What kinds of textures consumes most of your app's memory? Or how many memory usage do these textures cost? You don't need to compute by hand or just by guess.

There have the tool set right here for you. It's Apple's Instrument tools -- Allocations & Leaks.

You can long press Run command within Xcode and choose Profile to launch these two tools.

Here is the screenshot:



You can use Allocation tool to monitor your app's memory usage and Leaks to watch your memory leak.

You can also use some codes to get useful informations about game's memory usages.

Here it is:

1
2    CCTextureCache::sharedTextureCache()->dumpCachedTextureInfo();
When you call this code and run your game on DEBUG mode, then you will see some well formatted log message in your xcode's console window.

It looks like this:

1Cocos2d: cocos2d: "cc_fps_images" rc=5 id=3 256 x 32 @ 16 bpp => 16 KB
2Cocos2d: cocos2d: "XXX/hd/actor.pvr.ccz" rc=1059 id=4 2048 x 2048 @ 32 bpp => 16384 KB
3Cocos2d: cocos2d: CCTextureCache dumpDebugInfo: 2 textures, for 16400 KB (16.02 MB)
As you see, it shows us the texture's name , reference count , id, size and bit per pixel. And most importantly it shows use the memory usage. Here cc_fps_images consumes 16KB and actor.pvr.ccz consumes 16M memory.

Never do over-optimization.
This is a general optimization rule. When you do memory optimization, you should make some tradeoff. Because sometimes image quality and image memory usage are two opposite side.

Never never do over-optimization!

Memory Optimization Level
Here we split cocos2d-x memory optimization to three levels. On each level, we have different point of view and the strategies are also a little bit changing.

cocos2d-x client level
This is the most important optimization level we could pay attention to. Since we build game on top of cocos2d-x engine and the engine itself provides some sort of optimization options for use.

In this level, we can achieve the most value compare to our work time.

In short, we can't optimise textures , audios, fonts and particles' memory usage.

Firstly, let's cover texture optimization.
In order to optimise texture memory usage, we must know what factors affect texture's memory usage mostly.

There are three factors which influence textures' memory usage. It is texture's format(compressed or uncompressed), color depth and size.

We can use PVR format texture to reduce memory usage. The recommended texture format is pvr.ccz.

The more bit per color that texture use, the better image quality it will gain. But it will also consume more memory.

So we can use texture with color depth of RBGA4444 instead of RBGA8888, which costs as half memory of the latter one.

We also find that huge texture may also cause memory related problem. So you'd better use texture of moderate size.

Secondly,let's do something of audios.
There are also three factor that influence audio file's memory usage. It's audio file data format , bit rate and sample rate.

We prefer audio file with MP3 data format. Because it is supported by both android and ios platform. And it is also compressed and hardware accelerated.

You should keep your background music file with data size under 800KB. The simplest way is reduce background music time and repeat it in your program.

You should keep your audio file's sample rate around 96-128kbps and 44kHz bit rate is enough.

Last, we will talk about fonts and particles optimization.
Here we go with two small tips: when use BMFont to display your game score, choose the minimize number of character in your image file. For example,

If you want only display digit numbers , you can remove all the letters.

With particles, we could reduce it's memory usage by reduce particles number.

cocos2d-x engine level
If you are not a OpenGLES and game engine ninjia, you can simply leave it for us.

Since we are a open source game engine, if you have done some sort of optimization in engine level. Let us know please!

Any improvement or code commits will be welcome.

c++ language level
In this level, my advice is writing memory leak free code. Using the memory management rules built with cocos2d-x engine and try your best to avoid memory leaks.

Tips & Tricks
1. Load game assets frame by frame.

2. Reduce draw calls: Use CCSpriteBatchNode.

3. Load textures from largest to smallest.

4. Avoid high memory peak.

5. Use loading screen to preload game resources.

6. Release unused resources when needed

7. Release cached resource when received memory warnings.

8. use texturePacker to optimize texture’s size,format, color depth and so on.

9.Be careful to use JPGs!

10.Use 16-bit textures with RBGA4444 color depth.

11.Use NPOT textures instead of POT textures.

12.Avoid loading texture of huge size.

13. Prefer 1024*1024 NPOT pvr.ccz texture atlas over raw pngs.

Recommended Readings
Steffen Itterheim's cocos2d memory optimization tutorials
Apple's developer guide for reducing memory usage

