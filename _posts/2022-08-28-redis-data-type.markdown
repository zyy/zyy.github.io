---
layout:     post
title:      "Redis-5种基础数据类型详解"
date:       2022-08-28 14:25:00
author:     "yycoder"
header-img: "img/post-bg-unix-linux.jpg"
header-mask: 0.3
catalog:    true
tags:
- Redis
---

[](#redis入门：5种基础数据类型详解) Redis入门：5种基础数据类型详解
=========================================================

> Redis所有的key（键）都是字符串。我们在谈基础数据结构时，讨论的是存储值的数据类型，主要包括常见的5种数据类型，分别是：String、List、Set、Zset、Hash

[](#redis数据结构简介) Redis数据结构简介
-----------------------------

> Redis基础文章非常多，关于基础数据结构类型，我推荐你先看下[官方网站内容 (opens new window)](https://redis.io/topics/data-types)，然后再看下面的小结

首先对redis来说，所有的key（键）都是字符串。我们在谈基础数据结构时，讨论的是存储值的数据类型，主要包括常见的5种数据类型，分别是：String、List、Set、Zset、Hash。

![](/img/in-post/post-redis/img_2.png)


| 结构类型       | 结构存储的值        | 结构的读写能力                             |
|------------|---------------|-------------------------------------|
| String字符串  | 可以是字符串、整数或浮点数 | 对整个字符串或字符串的一部分进行操作；对整数或浮点数进行自增或自减操作 |
| List列表     | 一个链表，链表上的每个节点都包含一个字符串 | 对链表的两端进行push和pop操作，读取单个或多个元素；根据值查找或删除元素 |
| Set集合      | 包含字符串的无序集合 | 字符串的集合，包含基础的方法有看是否存在添加、获取、删除；还包含计算交集、并集、差集等 |
| Hash散列     | 包含键值对的无序散列表 | 包含方法有添加、获取、删除单个元素 |
| Zset有序集合   | 和散列一样，用于存储键值对 | 字符串成员与浮点数分数之间的有序映射；元素的排列顺序由分数的大小决定；包含方法有添加、获取、删除单个元素以及根据分值范围或成员来获取元素 |

[](#基础数据结构详解) 基础数据结构详解
-----------------------

> 内容其实比较简单，我觉得理解的重点在于这个结构怎么用，能够用来做什么？所以我在梳理时，围绕**图例**，**命令**，**执行**和**场景**来阐述。@pdai

### [](#string字符串) String字符串

> String是redis中最基本的数据类型，一个key对应一个value。

String类型是二进制安全的，意思是 redis 的 string 可以包含任何数据。如数字，字符串，jpg图片或者序列化的对象。

*   **图例**

下图是一个String类型的实例，其中键为hello，值为world

![](/img/in-post/post-redis/img_3.png)

*   **命令使用**


| 命令   | 简述        | 使用              |
|--------|---------------|-----------------|
| GET | 获取存储在给定键中的值 | GET name |
| SET | 设置存储在给定键中的值 | SET name value |
| DEL | 删除存储在给定键中的值 | DEL name |
| INCR | 将键存储的值加1 | INCR key |
| DECR | 将键存储的值减1 | DECR key |
| INCRBY | 将键存储的值加上整数 | INCRBY key amount |
| DECRBY | 将键存储的值减去整数 | DECRBY key amount |


*   **命令执行**

    ```shell
    127.0.0.1:6379> set hello world
    OK
    127.0.0.1:6379> get hello
    "world"
    127.0.0.1:6379> del hello
    (integer) 1
    127.0.0.1:6379> get hello
    (nil)
    127.0.0.1:6379> set counter 2
    OK
    127.0.0.1:6379> get counter
    "2"
    127.0.0.1:6379> incr counter
    (integer) 3
    127.0.0.1:6379> get counter
    "3"
    127.0.0.1:6379> incrby counter 100
    (integer) 103
    127.0.0.1:6379> get counter
    "103"
    127.0.0.1:6379> decr counter
    (integer) 102
    127.0.0.1:6379> get counter
    "102"
    ```

*   **实战场景**
    *   **缓存**： 经典使用场景，把常用信息，字符串，图片或者视频等信息放到redis中，redis作为缓存层，mysql做持久化层，降低mysql的读写压力。
    *   **计数器**：redis是单线程模型，一个命令执行完才会执行下一个，同时数据可以一步落地到其他的数据源。
    *   **session**：常见方案spring session + redis实现session共享，

### [](#list列表) List列表

> Redis中的List其实就是链表（Redis用双端链表实现List）。

使用List结构，我们可以轻松地实现最新消息排队功能（比如新浪微博的TimeLine）。List的另一个应用就是消息队列，可以利用List的 PUSH 操作，将任务存放在List中，然后工作线程再用 POP 操作将任务取出进行执行。

*   **图例**

![](/img/in-post/post-redis/img_4.png)

*   **命令使用**


| 命令   | 简述        | 使用              |
|--------|---------------|-----------------|
| RPUSH | 将给定值推入到列表右端 | RPUSH key value |
| LPUSH | 将给定值推入到列表左端 | LPUSH key value |
| RPOP | 从列表的右端弹出一个值，并返回被弹出的值 | RPOP key |
| LPOP | 从列表的左端弹出一个值，并返回被弹出的值 | LPOP key |
| LRANGE | 获取列表在给定范围上的所有值 | LRANGE key 0 -1 |
| LINDEX | 通过索引获取列表中的元素。你也可以使用负数下标，以 -1 表示列表的最后一个元素， -2 表示列表的倒数第二个元素，以此类推。 | LINDEX key index |

*   使用列表的技巧
    *   lpush+lpop=Stack(栈)
    *   lpush+rpop=Queue（队列）
    *   lpush+ltrim=Capped Collection（有限集合）
    *   lpush+brpop=Message Queue（消息队列）
*   **命令执行**

    ```shell
    127.0.0.1:6379> lpush mylist 1 2 ll ls mem
    (integer) 5
    127.0.0.1:6379> lrange mylist 0 -1
    1) "mem"
    2) "ls"
    3) "ll"
    4) "2"
    5) "1"
    127.0.0.1:6379> lindex mylist -1
    "1"
    127.0.0.1:6379> lindex mylist 10        # index不在 mylist 的区间范围内
    (nil)
    ```


*   **实战场景**
    *   **微博TimeLine**: 有人发布微博，用lpush加入时间轴，展示新的列表信息。
    *   **消息队列**

### [](#set集合) Set集合

> Redis 的 Set 是 String 类型的无序集合。集合成员是唯一的，这就意味着集合中不能出现重复的数据。

Redis 中集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是 O(1)。

*   **图例**

![](/img/in-post/post-redis/img_5.png)

*   **命令使用**


| 命令   | 简述        | 使用              |
|--------|---------------|-----------------|
| SADD | 向集合添加一个或多个成员 | SADD key value |
| SCARD | 获取集合的成员数 | SCARD key |
| SMEMBERS | 返回集合中的所有成员 | SMEMBERS key member |
| SISMEMBER | 判断 member 元素是否是集合 key 的成员 | SISMEMBER key member |

其它一些集合操作，请参考这里https://www.runoob.com/redis/redis-sets.html

*   **命令执行**


    127.0.0.1:6379> sadd myset hao hao1 xiaohao hao
    (integer) 3
    127.0.0.1:6379> smembers myset
    1) "xiaohao"
    2) "hao1"
    3) "hao"
    127.0.0.1:6379> sismember myset hao
    (integer) 1

*   **实战场景**
    *   **标签**（tag）,给用户添加标签，或者用户给消息添加标签，这样有同一标签或者类似标签的可以给推荐关注的事或者关注的人。
    *   **点赞，或点踩，收藏等**，可以放到set中实现

### [](#hash散列) Hash散列

> Redis hash 是一个 string 类型的 field（字段） 和 value（值） 的映射表，hash 特别适合用于存储对象。

*   **图例**

![](/img/in-post/post-redis/img_1.png)

*   **命令使用**


| 命令   | 简述        | 使用              |
|--------|---------------|-----------------|
| HSET | 添加键值对 | HSET hash-key sub-key1 value1 |
| HGET | 获取指定散列键的值 | HGET hash-key key1 |
| HGETALL | 获取散列中包含的所有键值对 | HGETALL hash-key |
| HDEL | 如果给定键存在于散列中，那么就移除这个键 | HDEL hash-key sub-key1 |

*   **命令执行**


    127.0.0.1:6379> hset user name1 hao
    (integer) 1
    127.0.0.1:6379> hset user email1 [email protected]
    (integer) 1
    127.0.0.1:6379> hgetall user
    1) "name1"
    2) "hao"
    3) "email1"
    4) "[email protected]"
    127.0.0.1:6379> hget user user
    (nil)
    127.0.0.1:6379> hget user name1
    "hao"
    127.0.0.1:6379> hset user name2 xiaohao
    (integer) 1
    127.0.0.1:6379> hset user email2 [email protected]
    (integer) 1
    127.0.0.1:6379> hgetall user
    1) "name1"
    2) "hao"
    3) "email1"
    4) "[email protected]"
    5) "name2"
    6) "xiaohao"
    7) "email2"
    8) "[email protected]"


*   **实战场景**
    *   **缓存**： 能直观，相比string更节省空间，的维护缓存信息，如用户信息，视频信息等。

### [](#zset有序集合) Zset有序集合

> Redis 有序集合和集合一样也是 string 类型元素的集合,且不允许重复的成员。不同的是每个元素都会关联一个 double 类型的分数。redis 正是通过分数来为集合中的成员进行从小到大的排序。

有序集合的成员是唯一的, 但分数(score)却可以重复。有序集合是通过两种数据结构实现：

1.  **压缩列表(ziplist)**: ziplist是为了提高存储效率而设计的一种特殊编码的双向链表。它可以存储字符串或者整数，存储整数时是采用整数的二进制而不是字符串形式存储。它能在O(1)的时间复杂度下完成list两端的push和pop操作。但是因为每次操作都需要重新分配ziplist的内存，所以实际复杂度和ziplist的内存使用量相关
2.  **跳跃表（zSkiplist)**: 跳跃表的性能可以保证在查找，删除，添加等操作的时候在对数期望时间内完成，这个性能是可以和平衡树来相比较的，而且在实现方面比平衡树要优雅，这是采用跳跃表的主要原因。跳跃表的复杂度是O(log(n))。

*   **图例**

![](/img/in-post/post-redis/img.png)

*   **命令使用**


| 命令   | 简述        | 使用              |
|--------|---------------|-----------------|
| ZADD | 将一个带有给定分值的成员添加到有序集合里面 | ZADD zset-key 178 member1 |
| ZRANGE | 根据元素在有序集合中所处的位置，从有序集合中获取多个元素 | ZRANGE zset-key 0-1 withccores |
| ZREM | 如果给定元素成员存在于有序集合中，那么就移除这个元素 | ZREM zset-key member1 |

更多命令请参考这里 https://www.runoob.com/redis/redis-sorted-sets.html

*   **命令执行**


    127.0.0.1:6379> zadd myscoreset 100 hao 90 xiaohao
    (integer) 2
    127.0.0.1:6379> ZRANGE myscoreset 0 -1
    1) "xiaohao"
    2) "hao"
    127.0.0.1:6379> ZSCORE myscoreset hao
    "100"


*   **实战场景**
    *   **排行榜**：有序集合经典使用场景。例如小说视频等网站需要对用户上传的小说视频做排行榜，榜单可以按照用户关注数，更新时间，字数等打分，做排行。
