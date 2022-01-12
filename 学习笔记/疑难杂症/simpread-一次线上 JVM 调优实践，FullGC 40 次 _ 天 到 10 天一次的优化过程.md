> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [mp.weixin.qq.com](https://mp.weixin.qq.com/s/UuXTh5i-yxBfHacsMHV-1Q)

********点击关注公众号，Java 干货****及时送达👇********

公众号

![](https://mmbiz.qpic.cn/mmbiz_png/ibnDyTicARr36uk7l4Ev9LvsKgBiaXXdEgEF8cfW59VMtwPGiaCwmPBdggQabfSLRtPSN9Q0MKicW0znMYkPlkltm0g/640?wx_fmt=png)

来源：blog.csdn.net/cml_blog/article/details/81057966

*   第一次优化
    
*   第二次优化
    
*   内存泄漏调查
    
*   第二次调优
    
*   总结
    

* * *

通过这一个多月的努力，将 FullGC 从 40 次 / 天优化到近 10 天才触发一次，而且 YoungGC 的时间也减少了一半以上，这么大的优化，有必要记录一下中间的调优过程。

对于 JVM 垃圾回收，之前一直都是处于理论阶段，就知道新生代，老年代的晋升关系，这些知识仅够应付面试使用的。前一段时间，线上服务器的 FullGC 非常频繁，平均一天 40 多次，而且隔几天就有服务器自动重启了，这表明的服务器的状态已经非常不正常了，得到这么好的机会，当然要主动请求进行调优了。未调优前的服务器 GC 数据，FullGC 非常频繁。

![](https://mmbiz.qpic.cn/mmbiz_png/ibnDyTicARr36uk7l4Ev9LvsKgBiaXXdEgEwM22a364cN6YnQGvpx0S7YvpJEMbwUSOfwia3K24uSls7bq91c57mEg/640?wx_fmt=png)图片

首先服务器的配置非常一般（2 核 4G），总共 4 台服务器集群。每台服务器的 FullGC 次数和时间基本差不多。其中 JVM 几个核心的启动参数为：

```
-Xms1000M -Xmx1800M -Xmn350M -Xss300K -XX:+DisableExplicitGC -XX:SurvivorRatio=4 -XX:+UseParNewGC -XX:+UseConcMarkSweepGC -XX:CMSInitiatingOccupancyFraction=70 -XX:+CMSParallelRemarkEnabled -XX:LargePageSizeInBytes=128M -XX:+UseFastAccessorMethods -XX:+UseCMSInitiatingOccupancyOnly -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+PrintHeapAtGC
```

*   -Xmx1800M：设置 JVM 最大可用内存为 1800M。
    
*   -Xms1000m：设置 JVM 初始化内存为 1000m。此值可以设置与 - Xmx 相同，以避免每次垃圾回收完成后 JVM 重新分配内存。
    
*   -Xmn350M：设置年轻代大小为 350M。整个 JVM 内存大小 = 年轻代大小 + 年老代大小 + 持久代大小。持久代一般固定大小为 64m，所以增大年轻代后，将会减小年老代大小。此值对系统性能影响较大，Sun 官方推荐配置为整个堆的 3/8。
    
*   -Xss300K：设置每个线程的堆栈大小。JDK5.0 以后每个线程堆栈大小为 1M，以前每个线程堆栈大小为 256K。更具应用的线程所需内存大小进行调整。在相同物理内存下，减小这个值能生成更多的线程。但是操作系统对一个进程内的线程数还是有限制的，不能无限生成，经验值在 3000~5000 左右。
    

第一次优化
-----

一看参数，马上觉得新生代为什么这么小，这么小的话怎么提高吞吐量，而且会导致 YoungGC 的频繁触发，如上如的新生代收集就耗时 830s。初始化堆内存没有和最大堆内存一致，查阅了各种资料都是推荐这两个值设置一样的，可以防止在每次 GC 后进行内存重新分配。基于前面的知识，于是进行了第一次的线上调优：提升新生代大小，将初始化堆内存设置为最大内存

```
-Xmn350M -> -Xmn800M
-XX:SurvivorRatio=4 -> -XX:SurvivorRatio=8
-Xms1000m ->-Xms1800m
```

将 SurvivorRatio 修改为 8 的本意是想让垃圾在新生代时尽可能的多被回收掉。就这样将配置部署到线上两台服务器（prod，prod2 另外两台不变方便对比）上后，运行了 5 天后，观察 GC 结果，YoungGC 减少了一半以上的次数，时间减少了 400s，但是 FullGC 的平均次数增加了 41 次。YoungGC 基本符合预期设想，但是这个 FullGC 就完全不行了。

![](https://mmbiz.qpic.cn/mmbiz_png/ibnDyTicARr36uk7l4Ev9LvsKgBiaXXdEgEbD8TdDu5ZSFwQJvJk3TxjfCjzIR2DhzU6fuGKribLxmmPcG0mYjvDDg/640?wx_fmt=png)图片

就这样第一次优化宣告失败。

第二次优化
-----

在优化的过程中，我们的主管发现了有个对象 T 在内存中有一万多个实例，而且这些实例占据了将近 20M 的内存。于是根据这个 bean 对象的使用，在项目中找到了原因：匿名内部类引用导致的，伪代码如下：

```
public void doSmthing(T t){ redis.addListener(new Listener(){  public void onTimeout(){   if(t.success()){    //执行操作   }  } });}
```

由于 listener 在回调后不会进行释放，而且回调是个超时的操作，当某个事件超过了设定的时间（1 分钟）后才会进行回调，这样就导致了 T 这个对象始终无法回收，所以内存中会存在这么多对象实例。

通过上述的例子发现了存在内存泄漏后，首先对程序中的 error log 文件进行排查，首先先解决掉所有的 error 事件。然后再次发布后，GC 操作还是基本不变，虽然解决了一点内存泄漏问题，但是可以说明没有解决根本原因，服务器还是继续莫名的重启。

内存泄漏调查
------

经过了第一次的调优后发现内存泄漏的问题，于是大家都开始将进行内存泄漏的调查，首先排查代码，不过这种效率是蛮低的，基本没发现问题。于是在线上不是很繁忙的时候继续进行 dump 内存，终于抓到了一个大对象

![](https://mmbiz.qpic.cn/mmbiz_png/ibnDyTicARr36uk7l4Ev9LvsKgBiaXXdEgE54Pmico7uHfyNTvtV0YiadYlbCCEtST8zG40XyMib2PnDVN4RZAJYEZibA/640?wx_fmt=png)图片![](https://mmbiz.qpic.cn/mmbiz_png/ibnDyTicARr36uk7l4Ev9LvsKgBiaXXdEgE34Xv4TXQaHRVK5jKqJ1m2T3IRic5XezicnicnGNicdnibfJYRA1nUfIFSbA/640?wx_fmt=png)图片

这个对象竟然有 4W 多个，而且都是清一色的 ByteArrowRow 对象，可以确认这些数据是数据库查询或者插入时产生的了。于是又进行一轮代码分析，在代码分析的过程中，通过运维的同事发现了在一天的某个时候入口流量翻了好几倍，竟然高达 83MB/s，经过一番确认，目前完全没有这么大的业务量，而且也不存在文件上传的功能。咨询了阿里云客服也说明完全是正常的流量，可以排除攻击的可能。

![](https://mmbiz.qpic.cn/mmbiz_jpg/ibnDyTicARr36uk7l4Ev9LvsKgBiaXXdEgEwZ2kcLU0QqfMIOOM6ibu6MORo5GPKmfYxX7Mt5SgUY4aF4nqtLpKQCA/640?wx_fmt=jpeg)图片

就在我还在调查入口流量的问题时，另外一个同事找到了根本的原因，原来是在某个条件下，会查询表中所有未处理的指定数据，但是由于查询的时候 where 条件中少加了模块这个条件，导致查询出的数量达 40 多万条，而且通过 log 查看当时的请求和数据，可以判断这个逻辑确实是已经执行了的，dump 出的内存中只有 4W 多个对象，这个是因为 dump 时候刚好查询出了这么多个，剩下的还在传输中导致的。而且这也能非常好的解释了为什么服务器会自动重启的原因。

解决了这个问题后，线上服务器运行完全正常了，使用未调优前的参数，运行了 3 天左右 FullGC 只有 5 次

![](https://mmbiz.qpic.cn/mmbiz_png/ibnDyTicARr36uk7l4Ev9LvsKgBiaXXdEgEV0uhZ5ibuseTmB1oKIjhxibpWMx1O61cUWjUp4okPnGSTqTrAAtuQvTg/640?wx_fmt=png)图片

第二次调优
-----

内存泄漏的问题已经解决了，剩下的就可以继续调优了，经过查看 GC log，发现前三次 GullGC 时，老年代占据的内存还不足 30%，却发生了 FullGC。于是进行各种资料的调查，在 https://blog.csdn.net/zjwstz/article/details/77478054 博客中非常清晰明了的说明 metaspace 导致 FullGC 的情况，服务器默认的 metaspace 是 21M，在 GC log 中看到了最大的时候 metaspace 占据了 200M 左右，于是进行如下调优，以下分别为 prod1 和 prod2 的修改参数，prod3，prod4 保持不变

```
-Xmn350M -> -Xmn800M
-Xms1000M ->1800M
-XX:MetaspaceSize=200M
-XX:CMSInitiatingOccupancyFraction=75
```

和

```
-Xmn350M -> -Xmn600M
-Xms1000M ->1800M
-XX:MetaspaceSize=200M
-XX:CMSInitiatingOccupancyFraction=75
```

prod1 和 2 只是新生代大小不一样而已，其他的都一致。到线上运行了 10 天左右，进行对比：prod1：

![](https://mmbiz.qpic.cn/mmbiz_png/ibnDyTicARr36uk7l4Ev9LvsKgBiaXXdEgE9LMRlibLuSiajILAIB4SanBrQm31b51hNae9pZytFFg24tq06YVwYXpg/640?wx_fmt=png)图片

prod2：

![](https://mmbiz.qpic.cn/mmbiz_png/ibnDyTicARr36uk7l4Ev9LvsKgBiaXXdEgEP8uGfqaVgWvubWcgfsz7ZrldXmzlypWR7fJf9DrtlhWUC7Wbc0DytQ/640?wx_fmt=png)图片

prod3：

![](https://mmbiz.qpic.cn/mmbiz_png/ibnDyTicARr36uk7l4Ev9LvsKgBiaXXdEgEVr7Qqf608KCQJ6ia8U7lrnFUEhia2DVkIE4V21g7IiaWotSrppKpSEdrw/640?wx_fmt=png)图片

prod4：

![](https://mmbiz.qpic.cn/mmbiz_png/ibnDyTicARr36uk7l4Ev9LvsKgBiaXXdEgEe6fnHpqpk4nNN8kspe6xOJUhic4Zic630Itia6RIiawenATAgEVHQbmaZA/640?wx_fmt=png)图片

对比来说，1，2 两台服务器 FullGC 远远低于 3，4 两台，而且 1，2 两台服务器的 YounGC 对比 3，4 也减少了一半左右，而且第一台服务器效率更为明显，除了 YoungGC 次数减少，而且吞吐量比多运行了一天的 3，4 两台的都要多（通过线程启动数量），说明 prod1 的吞吐量提升尤为明显。通过 GC 的次数和 GC 的时间，本次优化宣告成功，且 prod1 的配置更优，极大提升了服务器的吞吐量和降低了 GC 一半以上的时间。

prod1 中的唯一一次 FullGC：

![](https://mmbiz.qpic.cn/mmbiz_png/ibnDyTicARr36uk7l4Ev9LvsKgBiaXXdEgEEA7y5h9aDJhAVOJpicBVuT4OCJvIugkl9yk5vVsllEbibSkfksDyWUNA/640?wx_fmt=png)![](https://mmbiz.qpic.cn/mmbiz_png/8Jeic82Or04nBu5qyKR2VE1l1vGAibC1S4icsu3s9j17JoIHYvniaCO9eiaLB38NDSot7hPNMS2G4ywehvHPKaPCF0w/640?wx_fmt=png)

通过 GC log 上也没看出原因，老年代在 cms remark 的时候只占据了 660M 左右，这个应该还不到触发 FullGC 的条件，而且通过前几次的 YoungGC 调查，也排除了晋升了大内存对象的可能，通过 metaspace 的大小，也没有达到 GC 的条件。这个还需要继续调查，有知道的欢迎指出下，这里先行谢过了。

总结
--

通过这一个多月的调优总结出以下几点：

*   FullGC 一天超过一次肯定就不正常了
    
*   发现 FullGC 频繁的时候优先调查内存泄漏问题
    
*   内存泄漏解决后，jvm 可以调优的空间就比较少了，作为学习还可以，否则不要投入太多的时间
    
*   如果发现 CPU 持续偏高，排除代码问题后可以找运维咨询下阿里云客服，这次调查过程中就发现 CPU 100% 是由于服务器问题导致的，进行服务器迁移后就正常了。
    
*   数据查询的时候也是算作服务器的入口流量的，如果访问业务没有这么大量，而且没有攻击的问题的话可以往数据库方面调查
    
*   有必要时常关注服务器的 GC，可以及早发现问题
    

```
推荐阅读

• 2021年互联网公司“死亡”名单！（附清单）
• CompletableFuture：让你的代码免受阻塞之苦
• 超细！在浏览器输入xxxhub 回车之后发生了什么？
• 大文件上传：秒传、断点续传、分片上传

最近面试BATJ，整理一份面试资料《Java面试BAT通关手册》，覆盖了Java核心技术、JVM、Java并发、SSM、微服务、数据库、数据结构等等。

获取方式：点“在看”，关注公众号并回复 Java 领取，更多内容陆续奉上。

PS：因公众号平台更改了推送规则，如果不想错过内容，记得读完点一下“在看”，加个“星标”，这样每次新文章推送才会第一时间出现在你的订阅列表里。点“在看”支持一下吧！😀

```