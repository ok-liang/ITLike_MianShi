## 第一类

### Redis支持的数据类型

### 什么是Redis数据持久化？Redis有哪几种持久化方式，优缺点是什么?

### Redis的通讯协议(RESP)什么事RESP？有什么特点
### Redis有哪些架构模式，各个特点
### 什么是一致性哈希算法？什么是哈希槽

[文章一](https://www.cnblogs.com/lpfuture/p/5796398.html)

[文章二](https://blog.csdn.net/z15732621582/article/details/79121213)

### Redis常用命令
### 使用过Redis分布式锁吗/如何实现的
### 如果在setnx之后执行expire之前进程意外crash或者要重启维护了，那会怎么样？
### 使用过Redis做异步队列么，你是怎么用的？有什么缺点？
### 什么是缓存穿透？如何避免？什么是缓存雪崩？何如避免？

![](image/Xnip2019-02-01_00-32-04.png)

## 第二类

### 什么是Redis？简述它的优缺点？
### Redis相比memcached有哪些优势？
### Redis支持哪几种数据类型？
### Redis主要消耗什么物理资源？
### Redis的全称是什么？
### Redis有哪几种数据淘汰策略？
### Redis官方为什么不提供Windows版本？
### 一个字符串类型的值能存储最大容量是多少？
### 为什么Redis需要把所有数据放到内存中？
### Redis集群方案应该怎么做？都有哪些方案？
### Redis集群方案什么情况下会导致整个集群不可用
### MySQL里有2000w数据，redis中只存20w的数据，如何保证redis中的数据都是热点数据？
### Redis有哪些适合的场景
### Redis支持的Java客户端都有哪些？官方推荐用哪个？
### Redis和Redisson有什么关系？
### Jedis与Redisson对比有什么优缺点？
### Redis如何设置密码及验证密码？
### 说说Redis哈希槽的概念？
### Redis集群的主从复制模型是怎样的？
### Redis集群会有写操作丢失吗？为什么
### Redis集群之间是如何复制的？
### Redis集群最大节点个数是多少？
### Redis集群如何选择数据库？
### 怎么测试Redis的连通性？
### Redis中的管道有什么用？
### 怎么理解Redis事务？
### Redis事务相关的命令有哪几个？
### Redis key的过期时间和永久有效分别怎么设置
### Redis如何做内存优化
### Redis回收进程如何工作的？


![](image/Xnip2019-02-01_00-42-50.png)

## 第三类

### 使用Redis有哪些好处

(1) **速度快**，因为数据存在内存中，类似于HashMap，HashMap的优势就是查找和操作的时间复杂度都是O(1)

(2)**支持丰富数据类型**，支持string，list，set，sorted set，hash

(3) **支持事务**，操作都是原子性，所谓的原子性就是对数据的更改要么全部执行，要么全部不执行

(4) **丰富的特性**：可用于缓存，消息，按key设置过期时间，过期后将会自动删除

### Redis常见数据结构使用场景

![](image/Xnip2019-02-01_13-46-05.png)

### Mysql里面有2000万条数据，Redis只存有20W数据，如何保证Redis中的数据都是热点数据(redis有哪些数据淘汰策略)

> 相关知识：redis 内存数据集大小上升到一定大小的时候，就会施行数据淘汰策略（回收策略）。redis 提供 6种数据淘汰策略：  
 
1. volatile-lru：从已设置过期时间的数据集（server.db[i].expires）中挑选最近最少使用的数据淘汰 
2. volatile-ttl：从已设置过期时间的数据集（server.db[i].expires）中挑选将要过期的数据淘汰 
3. volatile-random：从已设置过期时间的数据集（server.db[i].expires）中任意选择数据淘汰 
4. allkeys-lru：从数据集（server.db[i].dict）中挑选最近最少使用的数据淘汰 
5. allkeys-random：从数据集（server.db[i].dict）中任意选择数据淘汰 
6. no-enviction（驱逐）：禁止驱逐数据

###  Redis并发竞争问题如何解决
Redis为单进程单线程模式，采用队列模式将并发访问编程串行访问。Redis本身没有锁的概念，Redis对于多个客户端并不存在竞争，但是Jedis客户端对Redis进行并发访问时会发生连接超时、数据转换错误、阻塞、哭护短关闭连接等问题，这些问题均是由于客户端连接混乱造成，对此有2种解决办法：  

+ 1.客户端角度，为保证每一个客户端正常有序的与Redis进行通信，对连接进行池化，同时对客户端对叙Redis操作采用内部锁synchronized。
+ 2.服务器角度，利用setnx实现锁。
  **注**对于第一种，需要应用程序自己处理资源的同步，可以使用的方法比较通俗，可以使用synchronized可以使用lock；第二种需要用到redis的setnx命令，但是需要注意一些问题。
  
### Redis与消息队列
不要使用redis去做消息队列，这不是redis的设计目标，但是太多人使用redis使用去做消息队列


redis做缓存，秒杀系统，首页缓存



## RedLock(redis分布式锁)

![](image/Xnip2019-02-01_13-40-34.png)


## 我所参与的系统使用redis用作：

`招聘行列`

招标以及投标


![](image/Xnip2019-02-13_13-31-04.png)


#Redis 面试题其他总结


![](image/Xnip2019-02-13_13-37-51.png)