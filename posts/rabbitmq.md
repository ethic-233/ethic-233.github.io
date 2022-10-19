---
title: 'RabbitMQ'
date: 2022-10-19 12:21:50
tags: [RabbitMQ]
published: true
hideInList: false
feature: 
isTop: false
---
![](https://ethic-233.github.io/post-images/1666155514000.jpg)# 消息队列

## MQ

### 什么是MQ

`MQ(message queue)`，是个队列，FIFO先入先出，只不过队列中存放的是message而已，还是一种跨进程的通信机制，用于上下游传递消息。在互联网架构中，MQ是一种非常常见的上下游“逻辑解耦+物理解耦”的消息通信服务。使用了MQ之后，消息发送上游只需要依赖MQ，不用依赖其他服务。

### 为什么要用MQ

1. 流量消耗

   举个例子，如果订单系统最多能处理一万次订单，这个处理能力应付正常时段的下单时绰绰有余，正常时段我们下单一秒后就能返回结果。但是在高峰期，如果有两万次下单操作系统是处理不了的，只能限制订单超过一万后不允许用户下单。使用消息队列做缓冲，我们可以取消这个限制，把一秒内下的订单分散成一段时间来处理，这时有些用户可能在下单十几秒后才能收到下单成功的操作，但是比不能下单的体验要好。

2. 应用解耦

   以电商应用为例，应用中有订单系统、库存系统、物流系统、支付系统。用户创建订单后，如果耦合调用库存系统、物流系统、支付系统，任何一个子系统出了故障，都会造成下单操作异常。当转变成基于消息队列的方式后，系统间调用的问题会减少很多，比如物流系统因为发生故障，需要几分钟来修复。在这几分钟的时间里，物流系统要处理的内存被缓存在消息队列中，用户的下单操作可以正常完成。当物流系统恢复后，继续处理订单信息即可，中单用户感受不到物流系统的故障，提升系统的可用性。

3. 异步处理

   有些服务间调用是异步的，例如A调用B，B需要花费很长时间执行，但是A需要知道B什么时候可以执行完，以前一般有两种方式，A过一段时间去调用B的查询`api`查询。或者A提供一个`callback api`,B执行完之后调用`api`通知A服务。这两种方式都不是很优雅，使用消息总线，可以很方便解决这个问题,A调用B服务后，只需要监听B处理完成的消息，当B处理完成后，会发送一条消息给MQ，MQ会将此消息转发给A服务。这样A服务既不用循环调用B的查询`api`，用做这些操作。A服务还能及时的得到异步处理成功的消息。

### MQ的分类

#### ActiveMQ

优点：单机吞吐量万级，时效性`ms`级，可用性高，基于主从架构实现高可用性，消息可靠性较低的概率丢失数据。

缺点：官方社区现在对ActiveMQ 5.x维护越来越少，高吞吐量场景较少使用。

#### Kafka

大数据的杀手锏，谈到大数据领域内的消息传输，则绕不开Kafka，这款为大数据而生的消息中间件，以其百万级TPS的吞吐量名声大噪，迅速成为大数据领域的宠儿，在数据采集、传输、存储的过程中发挥着举足轻重的作用。目前已经被LinkedIn，Uber，Twitter，Netflix等大公司所采纳。

优点：性能卓越，单机写入TPS约在百万条/秒，最大的优点，就是吞吐量高。时效性`ms`级可用性非常高，`kafka`是分布式的，一个数据多个副本，少数机器宕机，不会丢失数据，不会导致不可用，消费者采用`Pull`方式获取消息，消息有序，通过控制能够保证所有消息被消费且仅被消费一次；有优秀的第三方Kafka Web管理界面Kafka-Manager；在日志领域比较成熟，被多家公司和多个开源项目使用；功能支持：功能较为简单，主要支持简单的MQ功能，在大数据领域的实时计算以及日志采集被大规模使用。

缺点：Kafka单机超过64个队列/分区，Load会发生明显的飙高现象，队列越多，load越高，发送消息响应时间变长，使用短轮询方式，实时性取决于轮询间隔时间，消费失败不支持重试；支持消息顺序，但是一台代理宕机后，就会产生消息乱序，社区更新较慢。

#### RocketMQ

`RocketMQ`出自阿里巴巴的开源产品，用Java语言实现，在设计时参考了Kafka，并做出了自己的一些改进。被阿里巴巴广泛应用在订单，交易，充值，流计算，消息推送，日志流式处理，`binglog`分发等场景。

优点：单机吞吐量十万级，可用性非常高，分布式架构，消息可以做到0丢失，MQ功能较为完善，还是分布式的，扩展性好，支持10亿级别的消息堆积，不会因为堆积导致性能下降，源码是java我们可以自己阅读源码，定制自己公司的MQ。

缺点：支持的客户端语言不多，目前是java及C++，其中C++不成熟；社区活跃度一般，没有在MQ核心中去实现JMS等接口，有些系统要迁移需要修改大量代码。

#### RabbitMQ

2007年发布，是一个在AMQP(高级消息队列协议)基础上完成的，可复用的企业消息系统，是当前最主流的消息中间件之一。

优点：由于`erlang`语言的高并发特性，性能较好；吞吐量到万级，MQ功能比较完备、健壮、稳定、易用、跨平台、支持多种语言，如: Python、Ruby、.NET、Java、JMS、C、PHP、ActionScript、XMPP、STOMP等，支持AJAX文档齐全；开源提供的管理界面非常棒，用起来很好用，社区活跃度高；更新频率相当高。

缺点：商业版需要收费,学习成本较高。

### MQ的选择

#### `Kafka`

Kafka主要特点是基于Pull 的模式来处理消息消费，追求高吞吐量，一开始的目的就是用于日志收集和传输，适合产生大量数据的互联网服务的数据收集业务。大型公司建议可以选用，如果有日志采集功能,肯定是首选Kafka了。

#### `RocketMQ`

天生为金融互联网领域而生，对于可靠性要求很高的场景，尤其是电商里面的订单扣款，以及业务削峰，在大量交易涌入时，后端可能无法及时处理的情况。`RoketMQ`在稳定性上可能更值得信赖，这些业务场景在阿里双11已经经历了多次考验，如果你的业务有上述并发场景，建议可以选择`RocketMQ`。

#### `RabbitMQ`

结合erlang语言本身的并发优势，性能好时效性微秒级，社区活跃度也比较高，管理界面用起来十分方便，如果你的数据量没有那么大，中小型公司优先选择功能比较完备的RabbitMQ。

## RabbitMQ

RabbitMQ是一个消息中间件：它接受并转发消息。你可以把它当做一个快递站点，当你要发送一个包裹时，你把你的包裹放到快递站，快递员最终会把你的快递送到收件人那里，按照这种逻辑RabbitMQ是一个快递站，一个快递员帮你传递快件。RabbitMQ.与快递站的主要区别在于，它不处理快件而是接收，存储和转发消息数据。

### 四大核心概念

- 生产者

  产生数据发送消息的程序是生产者。

- 交换机

  交换机是RabbitMQ非常重要的一个部件，一方面它接收来自生产者的消息，另一方面它将消息推送到队列中。交换机必须确切知道如何处理它接收到的消息，是将这些消息推送到特定队列还是推送到多个队列，亦或者是把消息丢弃，这个得由交换机类型决定。

- 队列

  队列是RabbitMQ内部使用的一种数据结构，尽管消息流经RabbitMQ和应用程序，但它们只能存储在队列中。队列仅受主机的内存和磁盘限制的约束，本质上是一个大的消息缓冲区。许多生产者可以将消息发送到一个队列，许多消费者可以尝试从一个队列接收数据。这就是我们使用队列的方式。

- 消费者

  消费与接收具有相似的含义。消费者大多时候是一个等待接收消息的程序。请注意生产者，消费者和消息中间件很多时候并不在同一机器上。同一个应用程序既可以是生产者又是可以是消费者。

![图片说明](https://uploadfiles.nowcoder.com/images/20221013/428825176_1665594616855/27435235FA7684FF92CB1E800264346E "图片标题") 

### 各个名词介绍

`Broker`：接收和分发消息的应用，`RabbitMQ Server`就是`Message Broker`

`Virtual host`：出于多租户和安全因素设计的，把AMQP的基本组件划分到一个虚拟的分组中，类似于网络中的`namespace`概念。当多个不同的用户使用同一个`RabbitMQ server`提供的服务时，可以划分出多个`vhost`，每个用户在自己的`vhost`创建`exchange/queue`等。

`Connection`：`publisher/consumer`和`broker`之间的TCP连接。

`Channel`：如果每一次访问`RabbitMQ`都建立一个`Connection`，在消息量大的时候建立`TCP Connection`的开销将是巨大的，效率也低。`Channel`是在connection内部建立的逻辑连接，如果应用程序支持多线程，通常每个thread创建单独的channel进行通讯，`AMQP method`包含了`channel id`帮助客户端和`message broker`识别`channel`，所以`channel`之间是完全隔离的。Channel作为轻量级的Connection极大减少了操作系统建立TCP connection的开销。

`Exchange`：message到达broker的第一站，根据分发规则，匹配查询表中的`routing key`，分发消息到queue中去。常用的类型有：`direct(point-to-point)`，`topic(publish-subscribe) and fanout(multicast)`

`Queue`：消息最终被送到这里等待consumer取走

`Binding`：exchange和queue之间的虚拟连接，binding中可以包含routing key，Binding信息被保存到exchange中的查询表中，用于message的分发依据。

# 安装，启动

查找镜像

```shell
docker search rabbitmq
```

拉取镜像

```shell
docker pull rabbitmq:3-management
```

启动镜像

```shell
docker run --name rabbitmq  -d -p 5672:5672 -p 15672:15672 镜像ID
```

# Work Queues

工作队列（又称任务队列）的主要思想是避免立即执行资源密集型任务，而不得不等待他完成。相反我们安排任务在之后执行。我们把任务封装为消息并将其发送到队列。在后台运行的工作进程将弹出任务并最终执行作业。当有多个工作线程时，这些工作线程将一起处理这些任务。

# 消息应答

## 概念

消费者完成一个任务可能需要一段时间，如果其中一个消费者处理一个长的任务并仅只完成了部分突然它挂掉了，会发生什么情况。RabbitMQ一旦向消费者传递了一条消息，便z即将孩消息标记为删除。在这种情祝下，突然有个消费者挂掉了，我们将丢失正在处理的消息。以及后续发送给该消费这的消息，因为它无法接收到。为了保证消息在发送过程中不丢失，RabbitMQ引入消息应答机制，消息应答就是：消费者在接收到消息并且处理该消息之后，告诉RabbitMQ它已经处理了，RabbitMQ可以把该消息删除了。

## 自动应答

消息发送后立即被认为已经传送成功，这种模式需要在高吞吐量和数据传输安全性方面做权衡，因为这种模式如果消息在接收到之前，消费者那边出现连接或者channel关闭，那么消息就丢失了，当然另一方面这种模式消费者那边可以传递过载的消息，没有对传递的消息数量进行限制，当然这样有可能使得消费者这边由于接收太多还来不及处理的消息，导致这些消息的积压，最终使得内存耗尽，最终这些消费者线程被操作系统杀死，所以这种模式仅适用在消费者可以高效并以某种速率能够处理这些消息的情况下使用。

## 消息应答的方式

- `Channel.basicAck`（用于肯定确认）

  `RabbitMQ`已知道该消息并且成功的处理消息，可以将其丢弃了

- `Channel.basicNack`（用于否定确认）

- `Channel.basicReject`（用于否定确认）

  与`Channel.basicNack`相比少一个参数

  不处理该消息了直接拒绝，可以将其丢弃了

## Multiple的解释

手动应答的好处是可以批量应答并且减少网络拥堵。

```java
channel.basicAck(deliveryTag, true);
// 如果是true表示批量，如果是false表示不批量
```

## 消息自动重新入队

如果消费者由于某些原因失去连接（其通道已关闭，连接已关闭或TCP连接丢失），导致消息未发送ACK确认，RabbitMQ将了解到消息未完全处理，并将其重新排队。如果此时其他消费者可以处理，它将很快将其重新分发给另一个消费者。这样，即使某个消费者偶尔死亡，也可以确保不会丢失任何消息。

# RabbitMQ持久化

## 概念

刚刚我们已经看到了如何处理任务不丢失的情况，但是如何保障当RabbitMQ服务停掉以后消息生产者发送过来的消息不丢失。默认情况下RabbitMQ退出或由于某种原因崩溃时，它忽视队列和消息，除非告知它不要这样做。确保消息不会丢失需要做两件事：我们需要将队列和消息都标记为持久化。

## 队列如何实现持久化

之前我们创建的队列都是非持久化的，RabbitMQ如果重启的话，该队列就会被删除掉，如果要队列实现持久化，需要在声明队列的时候把durable参数设置为持久化。

```java
boolean durable = true;
channel.queueDeclare(ACK_QUEUE_NAME, durable, false, false, null);
```

但是需要注意的是如果之前声明的队列不是持久化的，需要把原先队列先删除，或者重新创建一个持久化的队列，不然就会出现错误。

## 消息实现持久化

要想让消息实现持久化需要在消息生产者修改代码，`MessageProperties.PERSISTENT_TEXT_PLAIN`添加这个属性。

把

```java
channel.basicPublish("", TASK_QUEUE_NAME, null, message.getBytes("UTF-8"));
```

改成

```java
channel.basicPublish("", TASK_QUEUE_NAME, MessageProperties.PERSISTENT_TEXT_PLAIN, message.getBytes("UTF-8"));
```

将消息标记为持久化并不能完全保证不会丢失消息。尽管它告诉RabbitMQ。将消息保存到磁盘，但是这里依然存在当消息刚准备存储在磁盘的时候但是还没有存储完，消息还在缓存的一个间隔点。此时并没有真正写入磁盘。持久性保证并不强，但是对于我们的简单任务队列而言，这已经绰绰有余了。

## 不公平分发

在最开始的时候我们学习到RabbitMQ分发消息采用的轮询分发，但是在某种场景下这种策略并不是很好，比方说有两个消费者在处理任务，其中有个消费者1处理任务的速度非常快，而另外一个消费者2处理速度却很慢，这个时候我们还是采用轮询分发的化就会到这处理速度快的这个消费者很大一部分时间处于空闲状态，而处理慢的那个消费者一直在干活，这种分配方式在这种情况下其实就不太好，但是RabbitMQ并不知道这种情况它依然很公平的进行分发。

为了避免这种情况，我们可以设置参数`channel.basicQos(1);`

意思就是如果这个任务我还没有处理完或者我还没有应答你，你先别分配给我，找目刖只能理一个任务，然后RabbitMQ就会把该任务分配给没有那么忙的那个空闲消费者，当然如果所有的消费者都没有完成手上任务，队列还在不停的添加新任务，队列有可能就会遇到队列被撑满的情况，这个时候就只能添加新的worker或者改变其他存储任务的策略。

## 预取值

本身消息的发送就是异步发送的，所以在任何时候，channel上肯定不止只有一个消息另外来自消费者的手动确认本质上也是异步的。因此这里就存在一个未确认的消息缓冲区，因此希望开友人员能限制此缓冲区的大小，以避免缓冲区里面无限制的未确认消息问题。这个时候就可以通过使用`basic.gos`，方法设置“预取计数”值来完成的。该值定义通道上允许的未确认消息的最大数量。一旦数量达到配置的数量，RabbitMQ将停止在通道上传递更多消息，除非至少有一个未处理的消息被确认，例如，假设在通道上有未确认的消息5，6，7，8，并且通道的预取计数设置为4，此时RabbitMQ将不会在该通道上再传递任何消息，除非至少有一个未应答的消息被ack。比方说tag=6这个消息刚刚被确认ACK，RabbitMQ将会感知这个情况到并再发送一条消息。消息应答和QoS,预取值对用户吞吐量有重大影响。通常，增加预取将提高向消费者传递消息的速度。虽然自动应答传输消息速率是最佳的，但是，在这种情况下已传递但尚未处理的消息的数量也会增加，从而增加了消费者的RAM消耗(随机存取存储器)应该小心使用具有无限预处理的自动确认模式或手动确认模式，消费者消费了大量的消息如果没有确认的话，会导致消费者连接节点的内存消耗变大，所以找到合适的预取值是一个反复试验的过程，不同的负载该值取值也不同100到300范围内的值通常可提供最佳的吞吐量，并且不会给消费者带来太大的风险。预取值为1是最保守的。当然这将使吞吐量变得很低，特别是消费者连接延迟很严重的情况下，特别是在消费者连接等待时间较长的环境中，对于大多数应用来说，稍微高一点的值将是最佳的。

# 发布确认

## 发布确认原理

生产者将信道设置成confirm模式，一旦信道进入confirm模式，所有在该信道上面发布的消息都将会被指派一个唯一的ID(从1开始)，一旦消息被投递到所有匹配的队列之后，broker就会发送一个确认给生产者(包含消息的唯一ID)，这就使得生产者知道消息已经正确到达目的队列了，如果消息和队列是可持久化的，那么确认消息会在将消息写入磁盘之后发出，broker回传给生产者的确认消息中delivery-tag域包含了确认消息的序列号，此外broker也可以设置basic.ack的multiple域，表示到这个序列号之前的所有消息都已经得到了处理。

confirm模式最大的好处在于他是异步的，一旦发布一条消息，生产者应用程序就可以在等信道返回确认的同时继续发送下一条消息，当消息最终得到确认之后，生产者应用便可以通过回调方法来处理该确认消息，如果RabbitMQ因为自身内部错误导致消息丢失，就会发送一条`nack`消息，生产者应用程序同样可以在回调方法中处理该`nack`消息。

## 发布确认的策略

### 开启发布确认的方法

发布确认默认是没有开启的，如果要开启需要调用方法`confirmSelect`，每当你想要使用发布确认，都需要在channel上调用该方法。

```java
Channel channel = connection.createChannel();
channel.confirmSelect();
```

### 单个确认发布

这是一种简单的确认方式，它是一种同步确认发布的方式，也就是发布一个消息之后只有它被确认发布，后续的消息才能继续发布，`waitForConfirmsOrDie(long)`这个方法只有在消息被确认的时候才返回，如果在指定时间范围内这个消息没有被确认那么它将抛出异常。

这种确认方式有一个最大的缺点就是：发布速度特别慢，因为如果没有确认发布的消息就会阻塞所有后续消息的发布，这种方式最多提供每秒不超过数百条发布消息的吞吐量，当然对于某些应用程序来说这可能已经足够了。

### 批量确认发布

上面那种方式非常慢，与单个等待确认消息相比，先发布一批消息然后一起确认可以极大地提高吞吐量，当然这种方式的缺点就是：当发生故障导致发布出现问题时，不知道是哪个消息出现问题了，我们必须将整个批处理保存在内存中，以记录重要的信息而后重新发布消息。当然这种方案仍然是同步的，也一样阻塞消息的发布。

### 异步确认发布

异步确认虽然编程逻辑比上两个要复杂，但是性价比高，无论是可靠性还是效率都没的说，他是利用回调函数来达到消息可靠性传递的，这个中间件也是通过函数回调来保证是否投递成功。

### 如何处理异步未确认消息

最好的解决方案就是把未确认的消息放到一个基于内存的能被发布线程访问的队列，比如说用`ConcurrentLinkedQueue`这个队列在`confirm callbacks`与发布线程之间进行消息的传递。

### 以上三种发布确认速度对比

单独发布消息：同步等待确认，简单，但吞吐量非常有限。

批量发布消息：批量同步等待确认，简单，合理的吞吐量，一旦出现问题很难推断出是哪条消息出现了问题。

异步处理：最佳性能和资源使用，在出现错误的情况下可以很好的控制，但是实现起来稍微难一些。

# 交换机

在上一节中，我们创建了一个工作队列。我们假设的是工作队列背后，每个任务都恰好交付给一个消费者(工作进程)。在这一部分中，我们将做一些完全不同的事情。我们将消息传达给多个消费者。这种模式称为“发布/订阅"。

为了说明这种模式，我们将构建一个简单的日志系统。它将由两个程序组成：第一个程序将发出日志消息，第二个程序是消费者。其中我们会启动两个消费者，其中一个消费者接收到消息后把日志存储在磁盘，另外一个消费者接收到消息后把消息打印在屏幕上，事实上第一个程序发出的日志消息将广播给所有消费者。

## Exchange

### 概念

RabbitMQ消息传递模型的核心思想是：生产者生产的消息从不会直接发送到队列。实际上，通常生产者甚至都不知道这些消息传递到了哪些队列中。

相反，生产者只能将消息发送到交换机（exchange），交换机工作的内容非常简单，一方面它接收来自生产者的消息，另一方面将他们推入队列。交换机必须确切知道如何处理收到的消息。是应该把这些消息放到特定队列还是说把他们放到许多队列中还是应该丢弃他们。这由交换机的类型来决定。

### 类型

直接（direct），主题（topic），标题（headers），扇出（fanout）

### 无名exchange

在本教程的前面部分我们对exchange一无所知，但仍然能够将消息发送到队列。之前能实现的原因是因为我们使用的是默认交换，我们通过空字符串（""）进行标识。

```java
channel.basicPublish("", "hello", null, message.getBytes());
```

第一个参数是交换机的名称。空字符串标识默认或无名称交换机：消息能从路由发送到队列中其实是由`routingKey(bindingkey)`绑定key指定的，如果它存在的话。

## 临时队列

每当我们连接到Rabbit时，我们都需要一个全新的空队列，为此我们可以创建一个具有随机名称的队列，或者能让服务器为我们选择一个随机队列名称那就更好了。其次一旦我们断开了消费者的连接，队列将被自动删除。

创建临时队列的方式如下：

```java
String queueName = channel.queueDeclare().getQueue();
```

## 绑定

什么是绑定（`bingding`）呢，`bingding`其实就是exchange和queue之间的桥梁，它告诉我们exchange和哪个队列进行了绑定关系。

## Fanout

它是将接收到的所有消息广播到它知道的所有队列中。系统中默认有些exchange类型。

## Direct exchange

在上一节中，我们构建了一个简单的日志记录系统。我们能够向许多接收者广播日志消息。在本节我们将向其中添加一些特别的功能，比方说我们只让某个消费者订阅发布的部分消息。例如我们只把严重错误消息定向存储到日志文件(以节省磁盘空间)，同时仍然能够在控制台上打印所有日志消息。

我们再次来回顾一下什么是 bindings，绑定是交换机和队列之间的桥梁关系。也可以这么理解：队列只对它绑定的交换机的消息感兴趣。绑定用参数：`routingKey`来表示。也可称该参数为`binding key`，创建绑定我们用代码：`channel.queueBind(queueName, EXCHANGE_NAME, "routingKey");`绑定之后的意义由其交换类型决定。

上一节中的我们的日志系统将所有消息广播给所有消费者，对此我们想做一些改变，例如我们希望将日志消息写入磁盘的程序仅接收严重错误(`erros`)，而不存储哪些警告(warning)或信息(info)日志消息避免浪费磁盘空间。Fanout这种交换类型并不能给我们带来很大的灵活性-它只能进行无意识的广播，在这里我们将使用direct这种类型来进行替换，这种类型的工作方式是，消息只去到它绑定的`routingKey`队列中去。

![图片说明](https://uploadfiles.nowcoder.com/images/20221013/428825176_1665594699086/6AFF7B77F992D55D774DA8C82FE088ED "图片标题") 

在上面这张图中，我们可以看到X绑定了两个队列,绑定类型是direct。队列Q1绑定键为orange队列Q2绑定键有两个：一个绑定键为black，另一个绑定键为green。

在这种绑定情况下，生产者发布消息到exchange上，绑定键为orange的消息会被发布到队列Q1。绑定键为`blackgreen`的消息会被发布到队列Q2，其他消息类型的消息将被丢弃。

## 多重绑定

![图片说明](https://uploadfiles.nowcoder.com/images/20221013/428825176_1665594721583/43D7CB07FA99B10FCFFCA5755EE22082 "图片标题") 

当然如果exchange的绑定类型是direct，但是它绑定的多个队列的key如果都相同，在这种情况下虽然绑定类型是direct但是它表现的就和fanout有点类似了，就跟广播差不多，如上图所示。

## Topics

### 之前类型的问题

在上一个小节中，我们改进了日志记录系统。我们没有使用只能进行随意广播的fanout交换机，而是使用了direct交换机，从而有能实现有选择性地接收日志。

尽管使用direct交换机改进了我们的系统，但是它仍然存在局限性。比方说我们想接收的日志类型有info.base和info.advantage，某个队列只想info.base的消息，那这个时候direct就办不到了。这个时候就只能使用topic类型。

### Topic的要求

发送到类型是topic交换机的消息的`routing_key`不能随便写，必须满足一定的要求，它必须是一个单词列表，以点号分隔开。这些单词可以是任意单词，比如说："`stock.usd.nyse`"，"`nyse.vmw`"，"`quick.orange.rabbit`"这种类型的。当然这个单词列表最多不能超过255个字节。

在这个规则列表中，其中有两个替换符是需要大家注意的

*（星号）可以代替一个单词

#（井号）可以代替零个或多个单词

### Topic匹配案例

中间带orange带3个单词的字符串（`*.orange.*`）

最后一个单词是rabbit的3个单词（`*.*.rabbit`）

第一个单词是lazy的多个单词（`lazy.#`）

# 死信队列

## 概念

先从概念解释上搞清楚这个定义，死信，顾名思义就是无法被消费的消息，字面意思可以这样理解，一般来说，producer将消息投递到broker或者直接到queue里了，consumer 从 queue取出消息进行消费，但某些时候由于特定的原因导致queue中的某些消息无法被消费，这样的消息如果没有后续的处理，就变成了死信，有死信自然就有了死信队列。

应用场景：为了保证订单业务的消息数据不丢失，需要使用到RabbitMQ的死信队列机制，当消息消费发生异常时，将消息投入死信队列中。还有比如说：用户在商城下单成功并点击去支付后在指定时间未支付时自动失效。

## 来源

消息TTL过期

队列达到最大长度（队列满了，无法再添加数据到mq中）

消息被拒绝（`basic.reject`或`basic.nack`）并且`requeue=false`

# 延迟队列

## 概念

延时队列，队列内部是有序的，最重要的特性就体现在它的延时属性上，延时队列中的元素是希望延时队列，队列内部是有序的，最重要的特性就体现在它的延时属性上，延时队列中的元素是希望。

## 延迟队列使用场景

1. 订单在十分钟之内未支付则自动取消
2. 新创建的店铺，如果在十天内都没有上传过商品，则自动发送消息提醒
3. 用户注册成功后，如果三天内没有登录则进行短信提醒
4. 用户发起退款，如果三天内没有得到处理则通知相关运营人员
5. 预定会议后，需要在预定的时间点前十分钟通知各个与会人员参加会议

这些场景都有一个特点，需要在某个事件发生之后或者之前的指定时间点完成某一项任务，如：发生订单生成事件，在十分钟之后检查该订单支付状态，然后将未支付的订单进行关闭；看起来似乎使用定时任务，一直轮询数据，每秒查一次，取出需要被处理的数据，然后处理不就完事了吗？如果数据量比较少，确实可以这样做，比如：对于"如果账单一周内未支付则进行自动结算"这样的需求，如果对于时间不是严格限制，而是宽松意义上的一周，那么每天晚上跑个定时任务检查一下所有未支付的账单，确实也是一个可行的方案。但对于数据量比较大，并且时效性较强的场景，如："订单十分钟内未支付则关闭"，短期内未支付的订单数据可能会有很多，活动期间甚至会达到百万甚至千万级别，对这么庞大的数据量仍旧使用轮询的方式显然是不可取的，很可能在一秒内无法完成所有订单的检查，同时会给数据库带来很大压力，无法满足业务要求而且性能低下。

# 整合SpringBoot

application.properties

```properties
spring.rabbitmq.host=182.92.234.71
spring.rabbitmq.port=5672
spring.rabbitmq.username=admin
spring.rabbitmq.password=123
```

# 队列TTL

创建两个队列QA和QB，两者队列TTL分别设置为10S和40S，然后在创建一个交换机X和死信交换机Y，它们的类型都是direct，创建一个死信队列QD，它们的绑定关系如下：

![图片说明](https://uploadfiles.nowcoder.com/images/20221013/428825176_1665594761447/5BE50809206E16E940C9835BB6540B40 "图片标题") 

延时队列优化：在这里新增了一个队列QC，绑定关系如下，该队列不设置TTL时间。

![图片说明](https://uploadfiles.nowcoder.com/images/20221013/428825176_1665594794406/3B9C83C9509F5052FE1446778DDB44EA "图片标题") 

看起来似乎没什么问题，但是在最开始的时候，就介绍过如果使用在消息属性上设置TTL的方式，消息可能并不会按时“死亡“，因为RabbitMQ只会检查第一个消息是否过期，如果过期则丢到死信队列,如果第一个消息的延时时长很长，而第二个消息的延时时长很短，第二个消息并不会优先得到执行。

# RabbitMQ插件实现延迟队列

上文中提到的问题，确实是一个问题，如果不能实现在消息粒度上的TTL，并使其在设置的TTL时间及时死亡，就无法设计成一个通用的延时队列。那如何解决呢，接下来我们就去解决该问题。

## 安装延时队列插件

在官网上

https://www.rabbitmq.com/community-plugins.html

下载`rabbitmq_delayed_message_exchange`插件，然后解压放置到RabbitMQ插件目录。进入RabbitMQ的安装目录下的plugins目录，执行下面命令让该插件生效，然后重启RabbitMQ。

`/usr/lib/rabbitmq/lib/rabbitmq_server-3.8.8/plugins`

```shell
rabbitmq-plugins enable rabbitmq_delayed_message_exchange
```

## 配置文件类代码

在我们自定义的交换机中，这是一种新的交换类型，该类型消息支持延迟投递机制，消息传递后并不会立刻投递到目标队列中，而是存储在`mnesia`（一个分布式数据系统）表中，当达到投递时间时，才投递到目标队列中。

## 总结

延时队列在需要延时处理的场景下非常有用，使用RabbitMQ来实现延时队列可以很好的利用RabbitMQ的特性，如：消息可靠发送、消息可靠投递、死信队列来保障消息至少被消费一次以及未被正确处理的消息不会被丢弃。另外，通过RabbitMQ集群的特性，可以很好的解决单点故障问题，不会因为单个节点挂掉导致延时队列不可用或者消息丢失。

当然，延时队列还有很多其它选择，比如利用Java的`DelayQueue`，利用Redis,的`zset`，利用Quartz或者利用Kafka的时间轮，这些方式各有特点,看需要适用的场景。
