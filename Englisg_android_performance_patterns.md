
2015年8月22日

##Android Performance Patterns

android性能优化的总结篇 ，原文链接找不到了……

===========

*The Google Developers YouTube channel has posted a set of 16 videos on Android Performance Patterns outlining a number of performance issues developers stumble across when creating applications for Android, along with advice on dealing with them which we will present in summary.*

*Rendering Performance 101. According to Colt McAnlis, presenter of the video series, improper rendering is the source of most performance issues on Android. If an activity needs more than 16ms to prepare the rendering of the next frame on the screen the system will drop the respective frame displaying instead the previous one. When frames are dropped the user may have an unpleasant experience using the application noticing scrolling that is not smooth or laggy transitions.*

*Some of the reasons for rendering performance issues are: redrawing a large view hierarchy, drawing many objects on top of each other, or repeated animations. McAnlis recommends a number of tools for investigating with such problems: Hierarchy Viewer, Traceview, Profile GPU Rendering, Debug GPU Overdraw, and GPU View Updates.*

*It is recommended to minimize the number of invalidations and layouts. The former are visualized with the Developer Option Show GPU View Updates, while the later can be investigated with Hierarchy Viewer.*

*Overdraw. Overdraw measures the number of times a certain pixel is drawn in a single frame. It is desirable for this counter to be 1, but this is not always possible. The Debug GPU Overdraw tool can show the areas that are mostly redrawn so the developer can know what to optimize.*

*VSYNC. Developers should pay attention to frame rate variations, especially when a frame rate higher than the refresh rate of the screen suddenly drops under it. This creates a visual lag in an animation that is easily perceived by the user.*

*Profile GPU Rendering. Accessible from the Developer Options setting, this tool generates graphs for all the Android activities active on the screen, usually the notification and navigation bars plus the active application. It is desirable to see frame rates under the 16ms threshold. The performance graph contains timing information for all major activities involved in rendering: updating a display list, executing a display list and processing glSwapBuffers.*

*60 fps. The desired rate of frames per second is minimum 60 fps which allows complex animations to be perceived well by the human eye. It is important to do 60 fps and avoid variations.*

*Custom Views. When the onDraw() method is overridden in order to create a custom view, the rendering process misses some Android overdraw optimizations such not drawing components that are completely covered by others. To minimize the lack of overdraw optimization, it is recommended to use canvas.clipRect() to specify the exact area that is custom drawn. Also, quickReject() should be used to determine if a region is outside the clipped rectangle, avoiding spending time drawing it when this is not necessary.*

*Memory Churn. Repeatedly allocating memory for lots of small memory objects which later are discarded forces the garbage collector to intervene multiple times, taking time from the 16ms window and possibly leading to frame dropping. Using the Android Studio Memory Monitor one can visualize GC activity and determine if there is too much garbage collection. The Allocation Tracker can then be used to determine where the memory objects are coming from. Fixing the related code may involve avoiding some memory allocations or moving them outside loops. Also, objects should not be allocated inside onDraw() because the method is called many times a second. When the application really needs a larger number of objects that are discarded shortly after, it is recommended to use a pool of objects that are created once and reused, thus avoiding the GC.*

*Memory Leaks. Memory leaks make GC take longer to complete which may impact the frame rate. To make sure an Android activity does not leak memory after the user leaves it, McAnlis recommends creating a blank activity with no or very little memory consumption, transition to it and force a garbage collection. Investigate memory consumption with the Heap and the Allocation Tracker tools in Android Studio before and after the transition to find out if the activity leaks memory or not.*

*Battery Consumption. According to a Purdue and Microsoft research paper (PDF), about 70% of the battery consumption of several high profile apps went to third party advertising modules which performed data analytics, location discovery and advertisement downloads. Only 30% of the battery went to the actual activity of the application. Google advises developers to be careful with battery consumption because this is on top of users’ concerns list. Top recommendation is avoiding waking a device from sleep unless absolutely necessary. If it has to be awaken, it is recommended using WakeLock with a timeout to make sure the device goes back to sleep in case something went wrong with the app. Another idea is to use AlarmManager.setInexactRepeating() to combine the wake up with another activity to save battery.*

*Battery consumption can be addressed by deferring some CPU intensive operations for a time when the device is connected to the charger, connected to WiFi or to combine multiple jobs in one device wake up. This can be done with the JobScheduler API which enables deferring some jobs to a later time. It is also recommended to perform network connections with care because after sending a network request and receiving a response, the device is kept awake for at least another 5 seconds just in case another data packet arrives from the server.*

*McAnlis also suggests using the consumption details offered by Settings|Battery and the Battery Historian tool to monitor how an app consumes battery over time.*