### Eureka

##### 自我保护机制：好死不如赖活着

总结：某时刻 某个微服务不可以用了，eureka 不会被立刻清理，依旧对该微服务的信息进行保存！

1. 默认，若eurekaServer在一定时间内没有收到某个微服务实例心跳，EurekaServer会注销该实例（默认90s）。但当网络分区发生故障，微服务Eureka之间无法正常通讯，以上行为可能变得非常危险 ，因为微服务本身非常健康，**不应该注销这个服务** 。Eureka自我保护机制来解决这个问题--当EurekaServer节点在短时间内丢失过多客户端时，那么这个节点就会进入自我保护机制，进而保护服务注册表中信息，不再注销微服务。网络故障恢复后，EurekaServer节点自行退出上述机制。

2. 自我保护模式中，ES不会注销任何服务实例。当收到的心跳数重新恢复到阈值以上时，则退出上述模式。

3. 总结：不会盲目注销任何健康微服务。

4. SpringCloud 自我保护机制开关：eureka.server.enable-self-perservation=faslse/true

   ```java
   hosts中增加地址映射
   server:
     port: 7003
   spring:
     application:
       #集群情况下，请保证application.name 相同，否则不可用（在eureka控制台unavailable-replicas中可以看到不可用的副本节点）
       name: clusterEureka
   eureka:
     instance:
       hostname: eureka3 # Eureka 服务端的实例名称
       prefer-ip-address: false
     client:
       register-with-eureka: true # 表示是否向eureka 注册自己
       fetch-registry: true   #　若为false 则表示自己为注册中心
       service-url:
   	  # defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
         # 集群写法如下：为了防止某eureka服务
         # eureka的server会把自己的注册信息与其他的server同步，
         # 所以这里我们不需要注册到自己身上，因为另外两台服务器会配置本台服务器
         defaultZone: http://eureka1:7001/eureka/,http://eureka2:7002/eureka/
   ```

5. ```
   # 而eureka是负载均衡消费者工程的。消费者拿到服务列表后，
   # 从其中按照负载均衡算法选择一个主机去访问
   ```



##### CAP原则

Consistency 强一致性，Availability 可用性，Partition tolerance 分区容错性

三个特性 最多只能同时实现两个点，不可能三者兼顾

###### Zookeeper保证了CP

​	在现实环境下，我们在注册中心查询服务列表，可以容忍注册中心返回几分钟之前的注册信息，但不能接受服务直接down掉不可用。所以，服务注册功能对可用性(**A**)的要求高于一致性(**C**)。

​	但ZK仅能保证CP。如master节点因为网络故障与其他节点失去联系时，剩余节点会重新进行leader选举。但该过程不仅耗时长长达30s-120s，而且整个zk集群不可用，导致了注册服务瘫痪。上述问题在云部署环境下发生的可能性较大，即使服务最终能够恢复，漫长的选举时间导致的注册长期无法使用是不可取的。



###### Eureka保证了AP

​	Ek则充分考虑了A，放弃了C，即优先保证了可用性，但可能拿到的数据不是最新的。**Ek各个节点是平等的**，任意几个节点挂掉不会影响正常节点的注册和查询服务。

​	Ek客户端在想某个Ek查询时，若链接失败则自动切换至其他节点。此外，Ek还有自我保护机制，即15分钟内超过85%的节点都没有正常心跳则启动该机制，Ek认为这是提供者与注册中心出现了网络故障。以下是可能会出现的情况：

1. Ek不再从注册列表中移除长时间没有心跳的服务。
2. Ek仍能够接受新服务的注册和查询需求，但不会被同步道其他节点上（）。
3. 网络稳定后，当前实例新的注册信息会被同步到其他节点上。

##### Ribbon

spring cloud ribbon 是基于Netflix Ribbon 实现的一套**客户端负载均衡工具**。

主要功能：提供客户端的软件负载均衡算法，将NetFlix的中间层服务连接在一起。Ribbon 客户端组件提供一系列完整的配置项如：链接超时、重试等等。即在配置文件中列出LoadBalancer（LB：负载均衡），Ribbon会自动帮助你基于某种规则（简单轮询，随机链接）去链接这些机器。我们也很容易使用Ribbon实现自定义的负载均衡算法。

what can this do？

	LB 负载均衡，常见LB软件有Nginx，Lvs，Apache+Tomcat等，就是要将用户请求平摊分配到多个服务上
	dubbo、Springcloud中均给我们提供了负载均衡，SpringCloud的负载均衡算法可以自定义
	LB简单分类：集中式LB(Nginx)，所有请求先集中后通过某种策略转发。进程式LB，将LB逻辑集成到消费方，消费方从服务注册中心(eureka)获知有哪些地址可用，然后再从这些地址中选出一个合适的服务器。Ribbon就属于进程内LB，它就继承于消费方。




### Feign

##### 调用微服务访问两种方法：

1.微服务名字 [Ribbon]

2.接口和注释[feign]

##### what can feign do?

Feign可使Java Htttp编写客户端更容易

​	前面方法：Ribbon+RestTemplate， 利用RT对Http请求的封装处理，形成了一套模板化的调用方法。但在实际开发中，由于对服务依赖的调用可能不止一处，一个接口会被多处调用，所以通常会针对每个微服务封装一些客户端类来包装这些依赖服务的调用。

​	在Feign的实现下，我们只需创建一个接口并使用注解来配置，即可完成对服务提供方的接口绑定，简化了Spring Cloud Ribbon。

```java
@Component
// 对该Service 指定 降级服务
@FeignClient(value = "SPRINGCLOUD-PROVIDER-DEPT",fallbackFactory = DeptClientServiceFallBackFactory.class )
// 与服务提供者的方法一致
public interface DeptClientService {
    @GetMapping("/dept/get/{id}")
    public Dept queryById(@PathVariable("id") Long id);
    @GetMapping("/dept/list")
    public List<Dept> queryAll();
    @PostMapping("/dept/add")
    public boolean addDept(Dept dept);

}

```



##### Hystrix

https://blog.csdn.net/IT__learning/article/details/121712616



###### 1.服务熔断





###### 2.服务降级

![image-20221005162932751](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221005162932751.png)



B、C服务都占用着 部分Hystrix资源 所以需要关闭，也就说 通过降级策略来告诉客户 目前的服务的状况。

Hystrix 熔断逻辑是部署在api上的，而consumer是需要开启hystrix服务的。

（在一台服务器上时）当关闭BC 服务时，A服务依旧可以访问，增加了A服务的资源，降低了A服务的压力。





服务熔断：服务端～    某个服务超时或者异常，引起熔断～，

服务降级：客户端～    释放某个服务器资源，为其他需要的服务器提供，从而降低服务器压力。

 此时在客户端（API中） 可以准备一个FallbackFactory，返回一个缺省值，虽然会使整体服务水平下降，但是能解决单个即将快要崩掉的服务压力。

###### Hystrix Dashboard

![image-20221005211722445](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221005211722445.png)

Success 成功请求                        Time out 超时数

Short-Circuited  熔断请求			Rejected 线程池拒绝数

Bad Request 错误请求				Failure 失败/异常数

0.0% 最近10s错误百分比  

流量越大 实心圆就越大，线是记录的2分钟内流量变化

使用方法：

另起一个项目或者在一个项目中启用 @EnableHystrixDashboard

便可以登录对应的端口号访问 /hystrix



对象要被监控的 项目启动类中添加

```java
//增加一个Servlet
@Bean
public ServletRegistrationBean hystrixMetricsStreamServlet(){
    ServletRegistrationBean servletRegistrationBean = new ServletRegistrationBean(new HystrixMetricsStreamServlet());
    servletRegistrationBean.addUrlMappings("/actuator/hystrix.stream");
    return servletRegistrationBean;
}
```



并在/hystrix 中添加url     http://localhost:8001/actuator/hystrix.stream

注意要在dashboard 项目中添加  

```
server:
  port: 9001
hystrix:
  dashboard:
    proxy-stream-allow-list: "*"
```

![image-20221005212325437](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221005212325437.png)



###  Zuul

https://zhuanlan.zhihu.com/p/268889371

Zuul是一个微服务网关，首先是一个微服务。也是会在Eureka注册中心中进行服务的注册和发现。也是一个网关，请求应该通过Zuul来进行路由。

Zuul网关不是必要的。**是推荐使用的。**

使用Zuul，**一般在微服务数量较多（多于10个）的时候推荐使用**，**对服务的管理有严格要求的时候**推荐使用，当**微服务权限要求严格**的时候推荐使用。

网关有以下几个作用：

- 统一入口：未全部为服务提供一个唯一的入口，网关起到外部和内部隔离的作用，**保障了后台服务的安全性**。
- 鉴权校验：**识别每个请求的权**限，**拒绝**不符合要求的请求。
- 动态路由：动态的将请求**路由到不同的后端集群中**。
- 减少客户端与服务端的耦合：服务可以独立发展，**通过网关层来做映射**。

what can this do？ 即上述的API Gateway



路由 和 过滤

路由：负责将外部请求转发到具体的微服务实例上，是实现外部访问 统一入口的基础。

过滤：负责将请求的处理过程进行干预，是实现 请求校验，服务聚合等功能的基础。

zuul与eureka整合：将zuul自身注册为Eureka服务治理下的应用，同时从Eureka中获得其他微服务消息，

以后访问其他微服务也都 需要经过 Zuul

需求：需要吧url地址上的 localhost:8001 隐藏，替换为www.xxx.com





# nacos

## 1.nacos与eureka的区别

eureka 的consumer如下

![image-20221222082110889](springcloud.assets/image-20221222082110889.png)

nacos

![image-20221222082425270](springcloud.assets/image-20221222082425270.png)



Feign 是声明式服务调用组件，其核心就是：像调用本地方法一样调用远程方法，无感知远程 HTTP 请求。让开发者调用远程接口就跟调用本地方法一样的体验，开发者完全无感知这是远程方法，无需关注与远程的交互细节，更无需关注分布式环境开发。



# BootStrap相关内容

```java
SpringBoot项目中application.yml的加载
SpringBoot项目默认加载application.yml
    
如果项目启动的时候传入 -Dspring.profiles.active=dev 项目在加载完application.yml后再去加载application-dev.yml，而且application-dev.yml中的配置会覆盖application.yml中的配置。
如果项目启动的时候没有传入 -Dspring.profiles.active=dev ，但是在application.yml中配置了 spring.profiles.active=dev，在加载完application.yml后再去加载application-dev.yml，而且application-dev.yml中的配置会覆盖application.yml中的配置。
```



Spring Boot 默认支持 properties(.properties) 和 YAML(.yml  .yaml ) 两种格式的配置文件，yml 和 properties 文件都属于配置文件，功能一样。Spring Cloud 构建于 Spring Boot 之上，在 Spring Boot 中有两种上下文，一种是 bootstrap，另外一种是 application，下面列举这两种配置文件的区别。

## 加载顺序

**若application.yml 和bootstrap.yml 在同一目录下：bootstrap.yml 先加载  application.yml后加载**

bootstrap.yml 用于应用程序上下文的引导阶段。bootstrap.yml 由父Spring ApplicationContext加载。

## 配置区别

bootstrap.yml 和 application.yml 都可以用来配置参数。

bootstrap.yml 用来程序引导时执行，应用于更加早期配置信息读取。可以理解成系统级别的一些参数配置，这些参数一般是不会变动的。一旦bootStrap.yml 被加载，则内容不会被覆盖。

application.yml 可以用来定义应用级别的， 应用程序特有配置信息，可以用来配置后续各个模块中需使用的公共参数等。

## 属性覆盖问题

启动上下文时，**Spring Cloud** 会创建一个 Bootstrap Context，作为 Spring 应用的 Application Context 的父上下文。

初始化的时候，Bootstrap Context 负责从外部源加载配置属性并解析配置。这两个上下文共享一个从外部获取的 Environment。**Bootstrap 属性有高优先级，默认情况下，它们不会被本地配置覆盖**。

也就是说如果加载的 application.yml 的内容标签与 bootstrap 的标签一致，application 也不会覆盖 bootstrap，而 application.yml 里面的内容可以动态替换。

## bootstrap.yml典型的应用场景

- 当使用 Spring Cloud Config Server 配置中心时，这时需要在 bootstrap.yml 配置文件中指定 spring.application.name 和 spring.cloud.config.server.git.uri，添加连接到配置中心的配置属性来加载外部配置中心的配置信息
- 一些固定的不能被覆盖的属性
- 一些加密/解密的场景







