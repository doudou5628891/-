![image-20221022222030718](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221022222030718.png)

![image-20221022225209764](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221022225209764.png)

![image-20221022225410722](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221022225410722.png)

![image-20221022225426418](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221022225426418.png)

# 1、学习目标

**什么是消息中间件**

**什么事协议**

**什么事持久化**

**消息分发**

**消息的高可用**

**消息的集群**

**消息的容错**

**消息的冗余**

# 2、什么是消息中间件



![image-20221022225802244](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221022225802244.png)

**单体架构**是大部分企业初期采用的架构模式，它的典型特点：是把所有的业务和模块，源代码，静态资源文件等都放在一个工程中，如果其中一个模块升级或者迭代发生一个很小的变动都会重新编译和重新部署项目。这种架构存在的问题就是：

**1：耦合度太高**

**2：运维的成本太高**

**3：不易维护**

**4：服务器成本高**

**5：以及升级架构的复杂度也会增大**

这样就会有后续的**分布式架构**如下

![image-20221022230407700](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221022230407700.png)

通俗讲：一个请求由

问题：

1.学习成本高，技术栈过多

2.运维成本、服务器成本和人员成本增高

3.项目的负载度也会上升

4.面临的错误和容错性也会成倍增加

5.占用的服务器端口和通讯的选择成本高

6.安全性的考虑和因素可能选择RMI/MQ相关的服务器端通讯

好处：

1.服务系统的独立，占用的服务器资源减少和占用的硬件成本减少，合理分配服务资源，不造成服务器资源浪费

2.系统的独立维护和部署，耦合度低

3.系统的架构和技术展的选择可变灵活（不是单纯选择java，订单系统用go，支付系统用php）

4.弹性的部署，不会造成平台因部署造成的瘫痪和停服的状态

# 3、消息中间件

1. 跨系统数据传递

2. 高并发流量削峰值

3. 数据的分发和异步处理

4. 大数据分析与传递

5. 分布式事务

   比如，10w并发请求订单，可以将订单请求堆积到消息队列中，能够稳健可靠的入库和执行。 

常见的消息中间件：ActiveMQ、RabbitMQ、Kafka、RocketMQ

https://zhuanlan.zhihu.com/p/150239985

![img](https://pic3.zhimg.com/v2-530cbd489e5846849674aa47c768831e_b.jpg)

## Nginx代理

Nginx是一个高性能的HTTP和反向代理web服务器，经常用在集群服务中 用作**统一代理层**和**负载均衡策略**。也可以作为**一层流量控制层**，一是**控制速率**，二是**控制并发连接数**

基于漏桶算法，提供**限制请求处理速率能力**；**限制IP的访问频率**，流量突然增大时，超出的请求**将被拒绝**；还可以限制并发连接数。

高并发秒杀场景下，Nginx层的各种限制策略，可以控制流量在一个相对稳定的状态

## CDN节点

CDN静态文件的代理节点，秒杀场景的服务有这样一个操作特点，活动倒计时开始之前，大量的用户会不断的刷新页面，**这时候静态页面可以交给CDN层面代理，分担数据服务接口的压力。**

CDN层面也可以做一层限流，在页面内置一层策略，**假设有10W用户点击抢购**，可以只放行1W的流量，**其他的直接提示活动结束即可**，这也是常用的手段之一。

话外之意：平时参与的抢购活动，可能你的请求根本没有到达数据接口层面，就极速响应商品已抢完，自行意会吧。

## 网关控制

网管层面处理服务接口路由，一些校验之外，最主要是可以继承一些**策略**进入网关，比如经过上述层层的流量控制后，请求已经接近核心的数据接口，**这时在网关层面内置一些策略控制**：如果活动是想激活老用户，网关层面快速判断用户属性，老用户会放行请求；如果活动的目的是拉新，则放行更多的新用户。

经过这些层面的控制，剩下的流量已经不多了，后续才真正开始执行抢购的数据操作。

话外之意：如果有10W人参加抢购活动，真正下沉到底层的抢购流量可能就1W，甚至更少，在分散到集群服务中处理。

## 并发熔断

在分布式服务的接口中，还有最精细的一层控制，对于一个接口在单位之间内控制请求处理的数量，这个基于接口的响应时间综合考虑，响应越快，单位时间内的并发量就越高，这里逻辑不难理解。

言外之意：流量经过层层控制，数据接口层面分担的压力已经不大，这时候就是面对秒杀业务中的加锁问题了。

## 分布式加锁

![image-20221023175731890](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221023175731890.png)

在实际的系统运行期间可能出现如下情况：线程01获取锁之后，进程被挂起，后续该执行的没有执行，锁失效后，线程02又获取锁，在数据库更新后，线程01恢复，此时在持有锁之后的状态，继续执行后就会容易导致数据错乱问题。

这时候就需要引入锁版本概念的，假设线程01获取锁版本1，如果没有执行，线程02获取锁版本2，执行之后，通过锁版本的比较，线程01的锁版本过低，数据更新就会失败。

总结：不论选择单体架构还是分布式架构都是项目开发的一个阶段，在什么阶段选择合适的架构方式，不能盲目追求，否则造成的后果都要自己买单。但作为一个开发人员学习新技术，探索与思考。必须要迎合市场的需求，发挥你的价值和所学的才能，创造价值和实现自我。

# 4、网络协议的三要素

![image-20221023182631650](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221023182631650.png)

![image-20221023182811621](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221023182811621.png)

负责数据**传递、存储、分发**

# AMQP协议

高级消息队列协议 Erlang语言，适合交换机领域，支持该协议的有RabbitMQ，ActiveMQ。

MQTT协议：Message Queueing Telemetry Transport消息队列是IBM开放的一个即时通讯协议，物联网系统架构重要组成部分

![image-20221023220033334](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221023220033334.png)



![image-20221023235352289](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221023235352289.png)

![image-20221023235603680](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221023235603680.png)

![image-20221023235908857](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221023235908857.png)



![image-20221024000118536](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221024000118536.png)

![image-20221024000228288](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221024000228288.png)



火车票出票系统用的多

![image-20221024002029739](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221024002029739.png)

![image-20221024002617073](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221024002617073.png)

## 什么是高可靠机制

![image-20221024002647003](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221024002647003.png)





# 5、Linux服务器安装RabbitMQ

https://www.lxlinux.net/5116.html 安装Erlang

https://blog.csdn.net/qq_24058757/article/details/91470009

https://stackoverflow.com/questions/51185691/rabbitmq-installation-error  官方安装方法

https://www.cnblogs.com/shanfeng1000/p/11951703.html ubuntu系统可以通过该方法安装

 

```
export PATH=$PATH:/opt/rabbitmq_server-3.8.1/sbin
source /etc/profile

```

# 6、RocketMq

![image-20230106104205313](../../资料/RabbitMQ.assets/image-20230106104205313.png)

## 6.1消息标识

Rocket 每个消息拥有唯一的MessageId，可携带具有业务标识的Key，以方便对消息的查询。

MessageId：

1.生产者send()消息时会自动生成一个MessageId（msgId），

producerIp+进程pid+messageClientIDSetter类的ClassLoader的hashCode+当前时间+AutomicInteger自增计数器

2.消息达到Broker后，Broker也会自动生成一个MessageId（offsetMsgId）

brokerIp+物理分区的offset



producer向一些队列轮流发送消息，队列集合成为Topic，

Consumer如果做广播消费，则一个Consumer实例消费这个Topic对应的所有队列，

Consumer如果做集群消费，则多个Consumer实例平均消费这个topic对应的队列集合；

RocketMQ主要由Producer、Broker、Consumer三部分组成，其中Producer负责生产消息，Consumer负责消费消息，Broker负责存储消息。**Broker在实际部署过程中对应一台服务器**，**每个Broker可以存储多个Topic的消息**，每个Topic的消息也可以分片存储于不同的Broker。



### Producer

负责生产消息，一般由业务系统负责生产消息。一个**消息生产者会把业务应用系统里产生的消息发送到broker服务器。**

RocketMQ提供多种发送方式，同步发送、异步发送、顺序发送、单向发送。同步和异步方式均需要Broker返回确认信息，单向发送不需要。



### Consumer

负责消费消息，一般是后台系统负责异步消费。一个**消息消费者会从Broker服务器拉取消息、并将其提供给应用程序**。

从用户应用的角度而言提供了两种消费形式：拉取式消费、推动式消费。

pull consumer: 通常主动调用Consumer的拉消息方法从Broker服务器拉消息、主动权由应用控制。一旦获取了批量消息，应用就会启动消费过程。

push consumer: 该模式下Broker收到数据后会主动推送给消费端，该消费模式一般实时性较高。

### Topic

表示一类消息的集合，每个主题包含若干条消息，每条消息只能属于一个主题，是RocketMQ进行消息订阅的基本单位。



### Broker Server

消息中转角色，负责存储消息、转发消息。

**代理服务器在RocketMQ系统中负责接收从生产者发送来的消息并存储、同时为消费者的拉取请求作准备**。

代理服务器也存储消息相关的元数据，包括消费者组、消费进度偏移和主题和队列消息等。

### Name Server

名称服务充当路由消息的提供者。

**生产者或消费者能够通过名字服务查找各主题相应的BrokerIP列表**。

**多个Namesrv实例组成集群，但相互独立，没有信息交换。**



### Producer Group

同一类Producer的集合，这类Producer发送同一类消息且发送逻辑一致。如果发送的是事务消息且原始生产者在发送之后崩溃，则Broker服务器会联系同一生产者组的其他生产者实例以提交或回溯消费。



### Consumer Group

同一类Consumer的集合，这类Consumer通常消费同一类消息且消费逻辑一致。

消费者组使得在消息消费方面，实现负载均衡和容错的目标变得非常容易。要注意的是，消费者组的消费者实例必须订阅完全相同的Topic。RocketMQ支持两种消息模式：集群消费（Clustering）和广播消费（Broadcasting）。

集群消费模式下,相同Consumer Group的每个Consumer实例平均分摊消息。

广播消费模式下,相同Consumer Group的每个Consumer实例都接收全量的消息。



## 6.2RocketMQ特性(features)

#### 订阅与发布

发布 是指某个生产者向某个topic发送消息；

订阅 是指某个消费者关注了某个topic中带有某些tag的消息，进而从该topic消费数据。



#### 消息过滤

RocketMQ的消费者可以根据Tag进行消息过滤，也支持[自定义属性](https://www.zhihu.com/search?q=自定义属性&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"528956421"})过滤。消息过滤目前是在Broker端实现的，优点是减少了对于Consumer无用消息的网络传输，缺点是增加了Broker的负担、而且实现相对复杂。



#### 消息可靠性

RocketMQ支持消息的高可靠，影响消息可靠性的几种情况：

1. Broker非正常关闭；
2. Broker异常Crash；
3. OS Crash；
4. 机器掉电，但是能立即恢复供电情况；
5. 机器无法开机（可能是cpu、主板、内存等关键设备损坏）；
6. 磁盘设备损坏；



1、2、3、4 四种情况都属于硬件资源可立即恢复情况，RocketMQ在这四种情况下能保证消息不丢，或者丢失少量数据（依赖刷盘方式是同步还是异步）。5、6属于单点故障，且无法恢复，一旦发生，在此单点上的消息全部丢失。RocketMQ在这两种情况下，通过异步复制，可保证99%的消息不丢，但是仍然会有极少量的消息可能丢失。通过同步双写技术可以完全避免单点，同步双写势必会影响性能，适合对消息可靠性要求极高的场合，例如与Money相关的应用。注：RocketMQ从3.0版本开始支持同步双写。



#### At least Once

Consumer先Pull消息到本地，消费完成后，才向服务器返回ack，如果没有消费一定不会ack消息，所以RocketMQ可以很好的支持此特性。

#### 回溯消费

回溯消费是指Consumer已经消费成功的消息，由于业务上需求需要重新消费，要支持此功能，Broker在向Consumer投递成功消息后，消息仍然需要保留。并且重新消费一般是按照时间维度，例如由于Consumer系统故障，恢复后需要重新消费1小时前的数据，那么Broker要提供一种机制，可以按照时间维度来回退消费进度。RocketMQ支持按照时间回溯消费，时间维度精确到毫秒。

#### 事务消息



#### 定时消息



#### 消息重试



#### 消息重投



#### 流量控制



#### 死信队列



#### 消息存储整体架构





### 路由发现

RMQ采用Pull模型。Topic路由信息发生变化，NS不会主动推送客户端。客户端定时拉取主题最新的路由。默认客户端每30s会拉取一次最新的路由。

push模型，长连接，适合实时性较高的

pull模型，实时性差

Long Pulling，长轮询模型

### 客户端NameServer选择策略

客户端是指Producer和Consumer

客户端连接的是哪个Nameserver？

客户端首先会产生一个随机数，然后与节点数量取模，得到的所要连接的结点索引，然后进行连接。如果连接失败则会采用Round-robin策略，逐个尝试连接其他节点

zookeeper的客户端选择zookeeperserver 是 两次shuffle，然后选第一个ip。

![image-20230110144815957](../../资料/RabbitMQ.assets/image-20230110144815957.png)

### Broker

消息中转角色，负责存储信息、转发信息。Broker在RocketMQ





![image-20230110145202667](../../资料/RabbitMQ.assets/image-20230110145202667.png)

 HA Service ：高可用服务。master和slave服务

Index Service：索引服务。

Remoting Module：整个Broker的实体
