# 2、容器功能

## 2.1组件注释种类

@Configuration

一般用来告诉Spring 该类是一个配置类 == 配置文件  .xml

```java
 @Configuration
public class MyConfig{
	@Bean //给容器添加组件，以方法名作为组件的id。返回类型就是组件类型。返回值就是组件在容器中的实例
	public User user01(){
		return new User("zhangsan",18);
	}
    
}

run.getBeanName("",xxx.class)
```

懒汉式与饿汉式的区别。是在与建立单例对象的时间的不同

懒汉：先判断是否为空，空则创建，否则直接return。若不加上synchronized则会导致对象的访问不是线程安全的。



```java
public class Singleton{
    private Singleton(){}
    private static Singleton singleton = null； //不建立对象
    public static synchronized Singleton getInstance(){
        if(singleton == null) { //先判断是否为空
            singleton = new Singleton (); //懒汉式做法
        }
        return singleton;
    }
}

```

饿汉：在开始就建立该单例对象。线程安全，以后不再改变

代理bean方法默认是单例

@Configuration(proxyBeanMethods==true)   full模式  单例 

@Configuration(proxyBeanMethods==false)  lite模式 多例 

full模式，每次外界调用某bean就会检查该bean是否存在。

lite模式，每次外界调用某bean不管bean是否存在，直接返回新的实例对象。



@Import 





基本使用

@Bean、@Component、@Controller、@Service、@Repository



@ComponentScan

```java
@SpringBootConfiguration
@EnableAutoConfiguration 
@ComponentScan("com.xxx.xxx")
public class MainApplication(String[] args){
   
}
```

@Import

给容器自动创建对应类型的组件

```java
@Import({xxx.class,DBHelper.class}) // 容器中存在com.xxx.xxx.bean.User   ch.qos.logback.core.db.DBHelper@sssxasdf  全类名
@Congfiguration(proxyBeanMethods=false)
public class MyConfig{
    
}
```

@Conditional

条件装配，满足Conditional指定的条件，则进行组件的注入

```java
 @Configuration
public class MyConfig{
	@ConditionalOnBean(name="tom")
    @Bean //给容器添加组件，以方法名作为组件的id。返回类型就是组件类型。返回值就是组件在容器中的实例
	public User user01(){
		return new User("zhangsan",18);
	}
    //@Bean("tom")
    //因为老师这里执行调用的顺序是先user01然后才到tom的，你执行user01时tom方法还没调用【组件还没有被注册】，所以结果是false
 	public Pet tomcatPet(){
        return new Pet("tomcat");
    }   
}
```

@ImportResource("classpath:beans.xml")   导入配置资源

@ConfigurationProperties

## 2.2 配置绑定

前缀统一时，使用该方法， 不同一时使用value

1. @Component+@ConfigurationProperties

```java
xxx.yaml or properties
xxx.sss = BYD
xxx.rrr = 111111
@Componenet 只有在容器中的组件Springboot才能提供
@ConfigurationProperties(prefix="xxx") // 前缀为xxx的配置
public class XXX {
    
}    

```

2. @EnableConfigurationProperties+@ConfigurationProperties //这种方式主要是因为，如果要引入第三方包，不改源码的情况下，还要加入该功能。

```java
@EnableConfigurationProperties(Car.class) //针对指定的car， 这样就不需要在xxx类上写@Component
public class MyConfig(){
    
}

```

