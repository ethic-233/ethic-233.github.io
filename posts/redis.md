---
title: 'Redis'
date: 2022-10-19 12:26:49
tags: [Redis]
published: true
hideInList: false
feature: 
isTop: false
---
![](https://ethic-233.github.io/post-images/1666155504315.gif)# 为什么用

用缓存技术来缓解数据库压力，优化数据库的结构和索引，Memcached只能缓解数据库的读取压力，master-slave模式使用主从复制技术来达到读写分离，提高读写性能和读库的可扩展性。在Memcached的高速缓存，MySQL的主从复制，读写分离的基础之上，MySQL主库的写压力开始出现瓶颈，而数据量的持续猛增，由于`MyISAM`使用表锁，在高并发下会出现严重的锁问题，大量的高并发MySQL应用开始使用`InnoDB`引擎代替`MyISAM`，同时，开始流行使用分表分库来缓解写压力和数据增长的扩展问题。

MySQL数据库也经常存储一些大文本字段，导致数据库表非常大，在做数据库恢复的时候非常慢，不容易快速恢复数据库，扩展性差，大数据下IO压力大，表结构更改困难。

今天我们可以通过第三方平台可以很容易的访问和抓取数据。用户的个人信息、社交网络、地理位置，用户生成的数据和用户操作日志已经成倍增加。如果要对这些用户数据进行挖掘，那SQL数据库已经不适合这些应用了，NoSQL数据库的发展却能很好的处理这些大的数据。

`NoSQL(NoSQL = Not Only SQL)`，意即"不仅仅是SQL"

泛指非关系型数据库，为了解决大规模数据集合多重数据种类带来的挑战，尤其是大数据应用难题，包括超大规模数据的存储。这些类型的数据存储不需要固定的模式，无需多余操作就可以横向扩展。NoSQL数据库种类繁多，但是一个共同的特点都是去掉关系数据库的关系型特性。数据之间无关系，这样就非常容易扩展，无形之间在架构层面带来了可扩展的能力。

一般MySQL使用Query Cache，每次表的更新Cache就失效，是一种大粒度的Cache，在针对web2.0的交互频繁的应用，Cache性能不高，而NoSQL的Cache是记录级的，是一种细粒度的Cache，所以NoSQL在这个层面上来说性能高多了。

多样灵活的数据模型：NoSQL无需事先为要存储的数据建立字段，随时可以存储自定义的数据格式，而在关系数据库里，增删字段是一件非常麻烦的事。

NoSQL

- 代表不仅仅是SQL
- 没有声明性查询语言
- 没有预定义的模式
- 键值对存储，列存储，文档存储，图形数据库
- 最终一致性，而非ACID属性
- 非结构化和不可预知的数据
- CAP定理
- 高性能，高可用性和可伸缩性

MongoDB是一个基于分布式文件存储的数据库，由C++语言编写，旨在为WEB应用提供可扩展的高性能数据存储解决方案。是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。

四大分类

- KV键值
- 文档型数据库
- 图关系数据库：它不是放图形的，放的是关系比如朋友圈社交网络、广告推荐系统、专注于构建关系图谱，Neo4J，`InfoGrid`

CAP原理

- 传统的ACID分别是什么

- CAP

  - C：Consistency（强一致性）
  - A：Availablity（可用性）
  - P：Partition tolerance（分区容错性）

- CAP的3进2

  CAP理论是说在分布式存储系统中，最多只能实现上面的两点，而由于当前的网络硬件肯定会出现延迟丢包等问题，所以，分区容忍性是我们必须实现的。所以我们只能在一致性和可用性之间进行权衡，没有NoSQL系统能同时保证这三点。

  C：强一致性

  A：高可用性

  P：分布式容忍性

  CA：传统Oracle数据库

  AP：大多数网站架构的选择

  CP：Redis，MongoDB

  注意：分布式架构的时候必须做出取舍

- 经典CAP图

  CAP理论的核心是：一个分布式系统不可能同时很好的满足一致性，可用性和分区容错性这三个需求，最多只能同时较好的满足两个。

  因此，根据CAP原理将NoSQL数据库分成了满足CA原则，满足CP原则和满足AP原则三大类。

  - CA：单点集群，满足一致性、可用性的系统，通常在可扩展性上不太强大
  - CP：满足一致性、分区容忍性的系统，通常性能不是特别高
  - AP：满足可用性、分区容忍性的系统，通常可能对一致性要求低一些

- BASE

  BASE是为了解决关系数据库强一致性引起的问题而引起的可用性降低而提出的解决方案。

  BASE其实是下面三个术语的缩写

  - 基本可用（`Basically Available`）
  - 软状态（`Soft state`）
  - 最终一致（`Eventually consistent`）

  它的思想是通过让系统放松对某一时刻数据一致性的要求来换取系统整体伸缩性和性能上改观，因为大型系统往往由于地域分布和极高性能的要求，不可能采用分布式事务来完成这些指标，要想获得这些指标，我们必须采用另一种方式来完成，这里BASE就是解决这个问题的办法。

- 分布式+集群简介

`Redis：REmote DIctionary Server(远程字典服务器)`

是完全开源免费的，用C语言编写的，遵守BSD协议，是一个高性能的（`key/value`）分布式内存数据库，基于内存运行，并支持持久化的NoSQL数据库，是当前最热门的NoSQL数据库之一，也被人们称为数据结构服务器。

Redis与其他key-value缓存产品有以下三个特点

- Redis支持数据的持久化，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用。
- Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，`zset`，hash等数据结构的存储。
- Redis支持数据的备份，即master-slave模式的数据备份。

能干嘛

- 内存存储和持久化：`redis`支持异步将内存中的数据写到硬盘上，同时不影响继续服务。
- 取最新N个数据的操作，如：可以将最新的10条评论的ID放在Redis的List集合里面。
- 模拟类似于`HttpSession`这种需要设定过期时间的功能
- 发布、订阅消息系统
- 定时器、计数器

# 基本知识

- 单进程
  - 单进程模型来处理客户端的请求、对读写等事件的响应是通过对epoll函数的包装来做到的，Redis的实际处理速度完全依靠主进程的执行效率
  - Epoll是Linux内核为处理大批量文件描述符而作了改进的epoll，是Linux下多路复用IO接口select/poll的增强版本，它能显著提高程序在大量并发连接中只有少量活跃的情况下的系统CPU利用率
- 默认16个数据库，类似数组下标从零开始，初始默认使用零号库
- Select命令切换数据库
- Dbsize查看当前数据库的key的数量
- Flushdb：清空当前库
- Flushall：通杀全部库
- 统一密码管理，16个库都是同样密码，要么都OK要么一个也连接不上
- Redis索引都是从0开始
- 为什么默认端口是6379

# 数据类型

- 五大数据类型

  - String（字符串）
  - Hash（哈希，类似Java里的Map）
  - List（列表）
  - Set（集合）
  - Zset（sorted set：有序集合）

- 哪里去获得redis常见数据类型操作命令

- 键（key）

  - `keys *`
  - `exist key的名字`，判断某个key是否存在
  - `move key db`，移除当前库
  - `expire key 秒钟`，为给定的key设置过期时间
  - `ttl key`，查看还有多少秒过期，-1表示永不过期，-2表示已经过期
  - `type key`，查看你的key是什么类型

- 字符串（String）

  String是redis最基本的类型，你可以理解成与Memcached一模一样的类型，一个key对应一个value

  String类型是二进制安全的，意思是redis的String可以包含任何数据，比如jpg图片或者序列化的对象

  String类型是Redis最基本的数据类型，一个Redis中字符串value最多可以是512M

  - 单值单value
  - `set/get/del/append/strlen`
  - `Incr/decr/incrby/decrby`，一定要是数字才能加减
  - `getrange/setrange`
  - `setex(set with expire)`键秒值`/setnx(set if not exist)`
  - `mset/mget/msetnx`
  - `getset(先get再set)`

- 列表（List）

  Redis列表是简单的字符串列表，按照插入顺序排序，你可以添加一个元素到列表的头部（左边）或者尾部（右边），它的底层是个链表

  - 是个字符串链表，left，right都可以插入添加
  - 如果键不存在，创建新的链表；如果键已存在，新增内容；如果键全部移除，对应的键也消失了；头和尾操作效率高

- 集合（Set）

  Redis的Set是string类型的无序集合，它是通过HashTable实现的

  - 单值多value
  - `sadd/smembers/sismember`
  - `scard`，获取集合里面的元素个数
  - `srem key value`，删除集合中元素
  - `srandmember key`，某个整数（随机出几个数）
  - `spop key`，随机出栈
  - `smove key1 key2`，在key里某个值，作业是将key1里的某个值赋给key2
  - 数学集合类
    - 差集：sdiff
    - 交集：sinter
    - 并集：sunion

- 哈希（Hash）

  是一个键值对集合，是一个String类型的field和value的映射表，hash特别适合用于存储对象，类似于Java的Map<String，Object>

  - KV模式不变，但V是个键值对
  - `hset/hget/hmset/hmget/hgetall/hdel`
  - `hlen`
  - `hexist key`，在key里面的某个值的key
  - `hkeys/hvals`
  - `hincrby/hincrbyfloat`
  - `hsetnx`

- 有序集合Zset（sorted set）

  Redis Zset和set一样也是String类型元素的集合，且不允许重复的成员

  不同的是每个元素都会关联一个double类型的分数

  Redis正是通过分数来为集合中的成员进行从小到大的排序，zset的成员是唯一的，但分数（score）却可以重复

  - 在set基础上加一个score值，之前set是k1 v1 v2 v3

    现在zset是k1 score1 v1 score2 v2

  - `zadd/zrange`

  - `zrangebyscore key`，开始score，结束score

    - `withscores`
    - `(`，不包含
    - `Limit`，作用是返回限制，Limit开始下标步，多少步

  - `zrem key`，某score下对应的value值，作用是删除元素

  - `zcard/zcount key score`区间`zrank key values`值，作用是获得下标值`/zscore key`对应值，获得分数

  - `zrerank key values`值，作用是逆序获得下标值

  - `zrevrange`

  - `zrerangebyscore key`，结束score开始score



