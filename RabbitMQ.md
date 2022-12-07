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

