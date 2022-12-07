# 业务 

**Runnable** 没有返回值、效率相比**java.util.concurrent.Callable**







## 线程与进程

**进程**：一个程序即一个进程，qq.exe Music.exe   

一个进程包含多个线程,至少包含一个线程

java 默认有几个线程？ 2个 ，main和GC

**线程**： 开个进程如word，自动保存（线程负责 ）

java 真的可以开启线程吗？ 不行

java若要启动线程.start()-> 需要先调用private native void start();本地方法，调用的底层的c++，java无法直接操作硬件。

并发编程：多线程操作同一个资源， 充分利用CPU资源（所有公司都很看重）

CPU一核、模拟出多条线程，快速交替 

```java
sout( Runtime.getRuntime().availableProcessors());
```

并行编程：

CPU多核，多个线程可以同时执行；线程池



java 中Thread.State枚举类有6个状态

NEW //创建

RUNNABLE // 运行

BLOCKED // 阻塞

WATING // 等待，死死等

TIMED_WAITING // 超时等待，有期限

TERMINATED // 终止

### 2.wait/sleep

##### 1、来自不同的类

wait=> Object

sleep => Thread

##### 2、关于锁的释放

wait会释放锁，sleep睡觉了，抱着锁睡觉，不释放

##### 3、使用的范围是不同的

wait 必须在同步代码块中使用

sleep可以在任何地方睡



## 3.lock锁

传统Synchronized                                                                                         

```java
基本的买票例子
    
//传统方法
public class SaleTicketDemo01{
	psvm(String[] args){
        new Thread(new MyThread()).start();
    }
}
class MyThread implements Runnable{
    @Override
    public void run(){
    }
}
/*
真正方法
线程就是一个单独的资源类，没有任何附属操作！
1、属性、方法
*/
public class SaleTicketDemo01{
	psvm(String[] args){
        //并发：多线程操作同一个资源类，把资源类丢入线程
        Ticket ticket = new Ticket();
        
        //@FunctionalInterface 函数式接口，即Thread。 Runnable 是个接口类
        new Thread(new Runnable(){
            @Override
            public void run(){
			}
        }).start();
        // lambda表达式 三元素 (参数) ->  {代码}
        //三个线程操作同一资源
        new Thread(()->{
            for 循环60次
            ticket.sale()
        },"A").start();
        new Thread(()->{
            ticket.sale()
        },"B").start();
        new Thread(()->{
            ticket.sale()
        },"C").start();
        
    }
}
//资源类OOP 面向对象编程
class Ticket{
    //属性方法
    private int number = 50;
    //卖票的方式   synchronized 
    
    
    
    public (synchronized这个是解决传统并发编程的问题) void sale(){
        if(number>0){
            sout(Thread.currentTread().getName+"第"+(number--)+"号票卖了，剩余"+number);
        }
    }
}
/*
nchronized本质
锁本身是个对象，synchronized这个关键字不是锁。只是个加锁的操作。
静态方法的锁是锁在了 类的class对象
普通方法的锁是 this 对象
针对同一个线程，synchronized锁可重入 也就是说一个syn方法可以调用另一个syn方法
子类同步方法可以调用父类同步方法吗  super.synFather() 可以


*/
```

