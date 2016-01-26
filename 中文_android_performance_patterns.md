
2015年8月22日

##Android Performance Patterns
##Android性能模式
===========

本文的翻译初衷是，虽然网络现有的翻译资料翻译的更好更直白明了，但是在读过英文原版后，你还是会发现一些新的东西，新的理解！

=====================

*The Google Developers YouTube channel has posted a set of 16 videos on Android Performance Patterns outlining a number of performance issues developers stumble across when creating applications for Android, along with advice on dealing with them which we will present in summary.*

**Googole 开发者YouTube频道 发布了一组16个视频讲述Android性能模式，列述一些开发者们在开发Android应用时困惑的性能问题，以及在总结中呈现了如何处理这些问题的建议。**

*Rendering Performance 101. According to Colt McAnlis, presenter of the video series, improper rendering is the source of most performance issues on Android. If an activity needs more than 16ms to prepare the rendering of the next frame on the screen the system will drop the respective frame displaying instead the previous one. When frames are dropped the user may have an unpleasant experience using the application noticing scrolling that is not smooth or laggy transitions.*

**渲染性能101 。 根据Colt McAnlis 提出的系列视频，在Android不好的渲染引起更多的性能问题。如果一个activity需要超过16ms来准备渲染下个画面显示在屏幕上，系统将会降低每个画面的显示而不是之前的一个。在使用应用时，画面卡顿是糟糕的体验。注意平滑的滚动和宽松的过渡。**

*Some of the reasons for rendering performance issues are: redrawing a large view hierarchy, drawing many objects on top of each other, or repeated animations. McAnlis recommends a number of tools for investigating with such problems: Hierarchy Viewer, Traceview, Profile GPU Rendering, Debug GPU Overdraw, and GPU View Updates.*

**渲染性能问题的原因有：重画大量的View层级，在最上层绘制许多交互的对象，或者重复的动画。McAnlis推荐大量的工具检查这样的问题：Hierarchy Viewer, Traceview, Profile GPU Rendering, Debug GPU Overdraw, and GPU View Updates.**

*It is recommended to minimize the number of invalidations and layouts. The former are visualized with the Developer Option Show GPU View Updates, while the later can be investigated with Hierarchy Viewer.*

**它建议尽量减少无效的布局。开发者选择显示GPU视图更新使前者形象化，而之后可以使用Hierarchy Viewer调查。**

*Overdraw. Overdraw measures the number of times a certain pixel is drawn in a single frame. It is desirable for this counter to be 1, but this is not always possible. The Debug GPU Overdraw tool can show the areas that are mostly redrawn so the developer can know what to optimize.*

**过度绘制。一个页面的某个像素点被多次的绘制。最理想的状态是绘制一次，但这并不是经常能做到的。调试GPU过度绘制工具可以显示被多次重绘的区域，这样开发者可以知道如何优化。**


*VSYNC. Developers should pay attention to frame rate variations, especially when a frame rate higher than the refresh rate of the screen suddenly drops under it. This creates a visual lag in an animation that is easily perceived by the user.*

**帧频率。开发者们应该花费注意力到帧率变化，特别是当帧率高于屏幕刷新频率的情况。这会造成一个很容易被用户捕捉的延迟动画效果。**

*Profile GPU Rendering. Accessible from the Developer Options setting, this tool generates graphs for all the Android activities active on the screen, usually the notification and navigation bars plus the active application. It is desirable to see frame rates under the 16ms threshold. The performance graph contains timing information for all major activities involved in rendering: updating a display list, executing a display list and processing glSwapBuffers.*

**GPU呈现模式分析。可以进入开发者选项，这个工具在所有活动的activity上生成图表，通常通知和导航栏附加在活动的应用程序上，它让人满体的地方在于可以看到16ms的临界值 : 性能图标包含所有主要活动参与的绘制计时信息：刷新显示列表、执行显示列表、加工 glSwapBuffers。**

*60 fps. The desired rate of frames per second is minimum 60 fps which allows complex animations to be perceived well by the human eye. It is important to do 60 fps and avoid variations.*

**60 fps。允许复杂动画被人眼感知的理想最低帧率是每秒60fps，最重要的是做到60fps和避免变更**

*Custom Views. When the onDraw() method is overridden in order to create a custom view, the rendering process misses some Android overdraw optimizations such not drawing components that are completely covered by others. To minimize the lack of overdraw optimization, it is recommended to use canvas.clipRect() to specify the exact area that is custom drawn. Also, quickReject() should be used to determine if a region is outside the clipped rectangle, avoiding spending time drawing it when this is not necessary.*

**自定义View。复写 *onDraw()* 方法以创建一个自定义View，绘制过程过遗漏了一些android过度绘制优化，诸如不画那些完全被遮住的组件。推荐使用 *canvas.clipRect()* 在指定区域自定义绘制，同样，如果超出了被裁剪的矩形范围，*quickReject()* 就应当决定被使用。当它不是必然的时候，避免花费过多的时间绘制。**

*Memory Churn. Repeatedly allocating memory for lots of small memory objects which later are discarded forces the garbage collector to intervene multiple times, taking time from the 16ms window and possibly leading to frame dropping. Using the Android Studio Memory Monitor one can visualize GC activity and determine if there is too much garbage collection. The Allocation Tracker can then be used to determine where the memory objects are coming from. Fixing the related code may involve avoiding some memory allocations or moving them outside loops. Also, objects should not be allocated inside onDraw() because the method is called many times a second. When the application really needs a larger number of objects that are discarded shortly after, it is recommended to use a pool of objects that are created once and reused, thus avoiding the GC.*

**内存波动。垃圾回收器将多次干预哪些大量创建又在短时间内马上被释放的对象，这将消耗界面的16ms，并可能导致帧率下降。使用Android studio 内存监控工具可以形象的看到GC活动和确定是否有过多的垃圾回收操作。工具 Allocation Tracker（分配跟踪器） 可以确定内存对象是来自哪里。定位关联代码可以避免潜在的内存分配或者移到循环外。并且，不应该在 *onDraw* 方法中分配内存对象，因为这个方法可能将会被调用多次，如果应用真的需要大量不久就将被销毁的对象，推荐使用只需创建一次之后可以重用的对象池，从而避免GC。**

*Memory Leaks. Memory leaks make GC take longer to complete which may impact the frame rate. To make sure an Android activity does not leak memory after the user leaves it, McAnlis recommends creating a blank activity with no or very little memory consumption, transition to it and force a garbage collection. Investigate memory consumption with the Heap and the Allocation Tracker tools in Android Studio before and after the transition to find out if the activity leaks memory or not.*

**内存泄露。内存泄露使得GC话费更长的时间，这有可能影响帧率。确保一个不在使用的Activity不会引起内存泄露。McAnlis推荐创建一个不消耗或消耗很少内存空的Activity，跳转它并手动执行GC方法。在Android Studio里的工具Heap and the Allocation Tracker tools 上研究跳转前后的内存消耗，找出activity是否发生了内存泄露。**

*（内存泄漏指的是那些程序不再使 的对象 法被GC识别,这样就导致这个对象 直留在内存当中,占 了宝贵 的内存空间。）*

Battery Consumption. According to a Purdue and Microsoft research paper (PDF), about 70% of the battery consumption of several high profile apps went to third party advertising modules which performed data analytics, location discovery and advertisement downloads. Only 30% of the battery went to the actual activity of the application. Google advises developers to be careful with battery consumption because this is on top of users’ concerns list. Top recommendation is avoiding waking a device from sleep unless absolutely necessary. If it has to be awaken, it is recommended using WakeLock with a timeout to make sure the device goes back to sleep in case something went wrong with the app. Another idea is to use AlarmManager.setInexactRepeating() to combine the wake up with another activity to save battery.

Battery consumption can be addressed by deferring some CPU intensive operations for a time when the device is connected to the charger, connected to WiFi or to combine multiple jobs in one device wake up. This can be done with the JobScheduler API which enables deferring some jobs to a later time. It is also recommended to perform network connections with care because after sending a network request and receiving a response, the device is kept awake for at least another 5 seconds just in case another data packet arrives from the server.

*McAnlis also suggests using the consumption details offered by Settings|Battery and the Battery Historian tool to monitor how an app consumes battery over time.*

**McAnlis 还建议使用提供消耗细节的电量审查工具（Battery Historian）来监控你的APP如何使用电池**

**最后推荐我的另外两篇文章 [Android性能编码规范](https://github.com/lizhaoxuan/Android-performance-norm) and [Android性能优化工具](https://github.com/lizhaoxuan/Android-performance-tool)**




