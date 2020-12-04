# 常用锁的分类

## 		排他锁/共享锁

```
共享锁和排他锁是从同一时刻是否允许多个线程持有该锁的角度来划分。
-------------------------------------------------------
排它锁（synchronized、ReentrantLock和Lock）
   	同一时刻，只允许一个线程去访问临界资源。
共享锁
   	同一时刻，允许多个线程进入持有锁，访问临界区资源;
   	在这里理解共享锁的时候，不是任意时刻都允许多线程持有共享锁的，而是在某些特殊情况下才允许多线程持有共享锁，在某些情况下不允许多个线程持有共享锁，否则，如果没有前提条件任意时刻都允许线程任意持有共享锁，则共享锁的存在无意义的。例如读写锁中的读锁，只有当没有写锁和写锁请求的时候，就可以允许多个线程同时持有读锁，但是不能对数据进行修改。这里的前提条件就是“没有写锁和写锁请求”，而不是任意时刻都允许多线程持有共享读锁。
```

```
tip:在这里说一个比较特殊的机制,ReentrantReadWriteLock 。它的内部含有两把锁，ReadLock 和 WriteLock，也就是一个读锁一个写锁，合在一起叫做读写锁。
	读锁是共享锁，写锁是独享锁。读锁的共享锁可保证并发读非常高效，而读写、写读、写写的过程互斥，因为读锁和写锁是分离的。所以ReentrantReadWriteLock的并发性相比一般的互斥锁有了很大提升。
	比如缓存，就需要读写锁来控制。缓存就是一个键值对，以下Demo模拟了缓存的读写操作，读的get方法使用了ReentrantReadWriteLock.ReadLock()，写的put方法使用了ReentrantReadWriteLock.WriteLock()。这样避免了写被打断，实现了多个线程同时读。代码如下：
```

```java
class MyCache{
    private volatile HashMap<String,String> map = new HashMap<>();
    private ReentrantReadWriteLock reentrantReadWriteLock = new ReentrantReadWriteLock();
    //写
    public void addEle(String key,String value){
        reentrantReadWriteLock.writeLock().lock();
        try{
            System.out.println(Thread.currentThread().getName() + "\t" + "正在写入： " + key);
            //为了增强显示效果
            try{ TimeUnit.SECONDS.sleep(1);}catch(Exception e){e.getStackTrace();};
            map.put(key,value);
            System.out.println(Thread.currentThread().getName() + "\t" + "写入完成······");
        }catch(Exception e){
            e.getStackTrace();
        }finally {
            reentrantReadWriteLock.writeLock().unlock();
        }
    }
    //读
    public void selectEle(String key){
        reentrantReadWriteLock.readLock().lock();
        try{
            System.out.println(Thread.currentThread().getName() + "\t" + "正在读取： " + key);
            String result = map.get(key);
            System.out.println(Thread.currentThread().getName() + "\t" + "读取完成========== " + result);
        }catch(Exception e){
            e.getStackTrace();
            reentrantReadWriteLock.readLock().unlock();
        }

    }
}

//测试类
public class ReadWriteLockTest {
    public static void main(String[] args) {
        MyCache myCache = new MyCache();
        //写
        for (int i = 0; i < 5; i++) {
            final String temp = i+"";
            new Thread(() ->{
                myCache.addEle(temp,temp);
            },"Write"+String.valueOf(i)).start();
        }
        //读
        for (int i = 0; i < 5; i++) {
            final String temp = i+"";
            new Thread(() ->{
                myCache.selectEle(temp);
            },"Read"+String.valueOf(i)).start();
        }
    }

}

```



## 悲观锁/乐观锁（[CAS详解及ABA问题的解决](https://blog.csdn.net/zz18435842675/article/details/106546419)）

```
较多地用于数据库的操作，是一种加锁的思维。
使用场景
​ 读取频繁使用乐观锁，写入频繁使用悲观锁。
-----------------------------------
悲观锁(数据库-行锁，表锁，写锁；java-Synchronized,ReentrantLock )
	默认当前操作是不安全的，当进行读写操作时，会进行加锁（排它锁）操作，优点是可以保证数据的安全性，但缺点是在高并发的场景下，会消耗硬件资源。
乐观锁。
	对于读操作时，不会加锁，但在进行写操作时，每次会追加一个版本号，使用cas的方式修改数据。在java中使用的实例（concurrenthashmap，concurrenthashmap使用分段锁，jdk1.8之后使用cas和synchronized方式）。
```

## 公平锁/非公平锁

```
这两个概念主要使用线程获取锁的顺序角度来区分的。
-----------------------------------------
公平锁（如：ReentrantLock(true)）
	  对于公平锁，所有等待的线程按照按照请求锁的先后顺序分别依次获取锁。
	  优点：很公平，所有的线程都能得到资源，不会造成线程饥饿。
	  缺点：并发度低，cpu唤醒阻塞线程的开销大。
       
非公平锁（如：synchronized和juc.ReentrantLock默认都是非公平锁）
      对于非公平锁，等待线程的线程获取锁的顺序和请求的先后不是对应关系。有可能是随机的获取锁，也有可能按照其他策略获取锁，总之不是按照FIFO的顺序获取锁
    在使用ReentrantLock的时候可以通过构造方法主动选择是实现公平锁还是非公平锁。
      优点：并发度高，减少CPU唤醒线程的开销。
      缺点：导致优先级反转或饥饿现象。
```

## 可重入锁/不可重入锁

```
这两个概念是从同一个线程在已经持有锁的前提下能否再次持有锁的角度来区分的。
----------------------------------------------------------------
可重入锁（ReentrantLock和Synchronized）
	  对于可重入锁，如果该线程已经获取到锁且未释放的情况下允许再次获取该锁访问临界区资源。此种情况主要是用在递归调用的情况下和不同的临界区使用相同的锁的		情况下。指的是同一个线程在外层方法获得锁时，进入内层方法会自动获取锁。白话来讲，可以这么形容，就像你有了家门的锁，厕所、书房、厨房就为你敞开了一样（即     便他们每个都有锁）。可重入锁可以避免死锁的问题。
不可重入锁（Lock）
     对于不可重入锁，则不允许同一线程在持有锁的情况下再次获取该锁并访问临界区资源。对于不可重入锁，使用的时候需要小心以免造成死锁（锁配对）。用自己的话来说，即便你有大门钥匙，厕所、书房、厨房你照样进不去。
     锁配对：锁之间要配对，加了几把锁，最后就得解开几把锁，锁的数量不匹配会导致死循环，从而导致死锁
```

	## 自旋锁/非自旋锁

```
这两种概念是从线程等待的处理机制来区分的。
使用场景
​ 锁的竞争不激烈，且占用锁时间非常短的代码块,因为自旋的消耗会小于线程阻塞挂起再唤醒的操作的消耗，这些操作会导致线程发生两次上下文切换！
​ 如果锁的竞争激烈，或者持有锁的线程需要长时间占用锁执行同步块，这时候就不适合使用自旋锁了;
------------------------------------
自旋锁
	 自旋锁在进行锁请求等待的时候不进行wait挂起，不释放CPU资源，执行while空循环。直至获取锁访问临界区资源。适用于等待锁时间较短的情景，如果等待时间较长，	则会耗费大量的CPU资源。而如果等待时间较短则可以节约大量的线程切换资源。
	 优点：减少线程切换的上下文开销，避免了用户进程和内核切换的消耗；
	 缺点：如果长时间上锁的话，自旋锁会非常耗费性能（消耗CPU，它阻止了其他线程的运行和调度）,可以通过设置自旋时间。
非自旋锁
     非自旋锁在进行锁等待的时候会释放CPU资源，可以通多sleep wait 或者CPU中断切换上下文，切换该线程。在线程等待时间较长的情况下可以选择此种实现机制。
     除此之外还有一种介于两者之间的锁机制——自适应自旋锁。当线程进行等待的时候先进性自旋等待，在自旋一定时间(次数)之后如果依旧没有持有锁则挂起等待。在jvm中synchronized锁已经使用该机制进行处理锁等待的情况。
在工作中可以根据不同的情况选取合适的锁进行使用。无论使用哪种锁，其目的都是保证程序能够按照要求顺利执行，避免数据混乱情况的发生。

```

## 分布式锁

### zookeeper分布式锁

​	--todo

### Redis分布式锁

​	--todo

![image-20201116112345311](C:\Users\kgcaox\AppData\Roaming\Typora\typora-user-images\image-20201116112345311.png)

​	

