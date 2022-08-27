# 线程状态与流转关系

### 一、线程的状态

1. 新建（New）：创建后尚未启动的线程状态&#x20;
2. 可运行（Runnable）：包含 Running（位于可运行线程池中）和 Ready（位于线程池中等待调度选中获取CPU使用权）&#x20;
3. 无限期等待（Waiting）：不会被分配CPU执行时间，需要显式唤醒&#x20;
4. 限期等待（Timed Waiting）：在一定时间后会有系统自动唤醒&#x20;
5. 阻塞（Blocked）：等待获取排它锁&#x20;
6. 结束（Terminated）：已终止线程的状态，线程已经结束执行

### 二、线程状态流转关系

对Java虚拟机中运行程序的每个对象来说都有这两个池：**锁池和等待池**

多线程时，需要调用这个对象的 Synchronized 方法或 Synchronized 块必须获得该对象锁，此时没有获取到锁的线程就进入锁池；而获取到锁的线程如果调用了 wait() 方法线程就会进入等待池，进入等待池的线程不会竞争该对象的锁。

<img src="/img/in-post/file.drawing.svg" alt="" class="gitbook-drawing">

![](/img/in-post/file.drawing.svg)
