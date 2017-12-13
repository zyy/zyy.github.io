---
layout:     post
title:      "JDK自带命令行JVM性能监控工具"
date:       2017-12-13 14:55:00
author:     "yycoder"
header-img: "img/post-bg-unix-linux.jpg"
header-mask: 0.3
catalog:    true
tags:
    - JVM
    - Java
    - jps
    - jstat
    - jmap
    - jinfo
    - jmap
    - jstack
---

>当系统出bug需要定位问题的时候，知识、经验是关键基础，数据是依据，工具是运用知识处理数据的手段。这里所说的数据包括：运行日志，异常堆栈，GC日志，线程快照（threaddump/javacore文件），堆转储快照（heapdump/hprof文件）等。使用适当的虚拟机监控和分析工具可以加快我们分析数据、定位问题的速度。

# JDK的命令行工具 

JDK本身提供了很多方便的JVM性能监控工具，除了集成式的VisualVM和jConsole外，还有jps、jstack、jmap、jhat、jstat等小巧的工具。它们在JDK的bin目录之下

# jps 

jps（JVM Process Status Tool）用来查看JVM里面所有进程的具体状态, 包括进程ID，进程启动的路径等等。

命令格式

    > jps [options] [hostid]
    
jps常用options如下表：

|选项|作用|
|----|----|
|-q  |只输出LVMID，省略主类的名称|
|-m  |输出虚拟机进程启动时传递给主类main()函数的参数|
|-l  |输出主类的全名，如果进程执行的是jar，输出jar路径|
|-v  |输出虚拟机进程启动时JVM参数|

# jstat

jstat（JVM Statistics Monitoring Tool）是用于监控虚拟机各种运行状态信息的命令行工具。它可以显示本地或远程虚拟机中的类装载、内存、垃圾收集、JIT编译等运行数据。

命令格式

    > jstat [option vmid [interval count]]
    
参数说明

- option – 选项，代表用户希望查询的虚拟机信息，主要分为3类：类装载、垃圾收集、运行期编译状况。
- vmid – VM的进程号，即当前运行的java进程号
- interval – 间隔时间，单位为秒或者毫秒
- count – 打印次数，如果缺省则打印无数次

jstat 常用option 如下表：

|选项|作用|
|----|----|
|-class|监视类装载、卸载数量、总空间以及装载类所耗费的时间
|-gc|监视java堆状况，包括Eden区、两个Survivor区、年老代、永久代等的容量、已用空间、GC时间合计等信息
|-gccapacity|监视内容与-gc基本相同，但输出主要关注java堆各个区域使用到的最大、最小空间
|-gcutil|监视内容与-gc基本相同，但输出主要关注已使用空间占总空间的百分比
|-gccause|与-gcutil功能一样，但是会额外输出导致上一次GC产生的原因
|-gcnew|监视新生代GC状况
|-gcnewcapacity|监视内容与-gcnew基本相同，输出主要关注使用到的最大、最小空间
|-gcold|监视老年代GC状况
|-gcoldcapacity|监视内容与-gcold基本相同，输出主要关注使用到的最大、最小空间
|-gcpermcapacity|输出永久代使用到的最大、最小空间
|-compiler|输出JIT编译器编译过的方法、耗时等信息
|-printcompilation|输出已经被JIT编译器编译的方法

输出内容含义如下： 

- S0 – Heap上的 Survivor space 0 区已使用空间的百分比
- S1 – Heap上的 Survivor space 1 区已使用空间的百分比
- E – Heap上的 Eden space 区已使用空间的百分比
- O – Heap上的 Old space 区已使用空间的百分比
- P – Perm space 区已使用空间的百分比
- YGC – 从应用程序启动到采样时发生 Young GC 的次数
- YGCT – 从应用程序启动到采样时 Young GC 所用的时间(单位秒)
- FGC – 从应用程序启动到采样时发生 Full GC 的次数
- FGCT – 从应用程序启动到采样时 Full GC 所用的时间(单位秒)
- GCT – 从应用程序启动到采样时用于垃圾回收的总时间(单位秒)
    
示例:

参数interval和count代表查询间隔和次数，如果省略这两个参数，说明只查询一次。如下：

    jstat -gcutil 25444

每500毫秒查询一次进程25444 垃圾收集状况，一共查询10次，如下：

    jstat -gcutil 25444 500 10

    jstat -gcnewcapacity 25444 

    jstat -gcold 25444 

jstat -class pid：显示加载class的数量，及所占空间等信息。

    jstat -class 25444 

jstat -compiler pid:显示VM实时编译的数量等信息。

    jstat -compiler 25444 
    
# jinfo

jinfo（Configuration Info for Java）可观察运行中的java程序的运行环境参数：参数包括Java System属性和JVM命令行参数；也可从core文件里面知道崩溃的Java应用程序的配置信息。

命令格式

    > jinfo [option] pid
    
# jmap

jmap（Memory Map for Java）命令用于生成堆转储快照（一般称为heapdump或dump文件）。

命令格式

    > jmap [option] vmid
    
option选项如下表所示：

|选项|作用
|---|---|
|-heap|显示jvm heap详细信息
|-histo|显示jvm heap中对象统计信息，包括类，实例数量，合计容量
|-dump|生成Java堆转储快照。格式为：-dump:[live],format=b,file=filename，其中live子参数说明是否只dump出存活的对象

示例

观察到java heap的内存使用情况

    jmap -heap 2083

观察heap中所有对象的情况，包括对象数量和所占空间大

    jmap -histo 2083 
    jmap -histo:live 2083

dump出所有对象文件可用于进一步分析

    jmap -dump:format=b,file=heap.bin 2083 

dump出存活的对象文件可用于进一步分析

    jmap -dump:live,format=b,file=heap.bin 2083
    
# jstack

jstack （Stack Trace for Java）命令用于生成虚拟机当前时刻的线程快照（一般称为threaddump）。线程快照就是当前虚拟机内每一条线程正在执行的的方法堆栈的集合，生成线程快照的主要目的是定位线程出现长时间停顿的原因，如线程间的死锁、死循环、请求外部资源导致的长时间等待 等都是导致线程长时间停顿的原因。

命令格式

    > jstack [option] vmid
    
option选项如下表所示：

|选项|作用
|---|---|
|-F|当正常输出的请求不被响应时，强制输出线程堆栈
|-l|除堆栈外，显示关于锁的附加信息
|-m|如果调用到本地方法的话，可以显示C/C++的堆栈