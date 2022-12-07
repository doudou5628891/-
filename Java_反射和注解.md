## 元注解

```java
@Target(value = {ElementType.METHOD,ElementType.Type})  //表示注解可以应用在方法和类上
@interface MyAnnotation{
    
}
@Retention(value=RetentionPolicy.RUNTIME) //表示我们的注解在在什么地方还有效
@interface MyAnnotation{
    
}

@Documented //表示是否将我们的注解生成在JAVAdoc中

@Inherited	//子类可以继承父类的注解
@interface MyAnnotation{
    
}
```

## 自定义注解

@interface 调用该方法 可以自动继承java.lang.annotation.Annotation接口 

格式为：public @interface 注解名｛定义内容｝

```java
public class Test03{
    @MyAnnotation(name="任炜",schools={"山西大学"，"太理"})
    public void test(){}
    
    @MyAnnotation("任炜")
    public void test2(){}
}
@Target({Element.Type,Element.Method})
@Retention(RetentionPolicy.RUNTIME)
@interface MyAnnotation{
    //注解的参数：参数类型+参数名();
    String name() default "";
    int age() default 0;
    int id() default -1;	//如果默认值-1，代表不存在
    String[] schools() default {"",""};
    
}

@Target({Element.Type,Element.Method})
@Retention(RetentionPolicy.RUNTIME)
@interface MyAnnotation2{
    String value();//只有value时 注解可以省略参数名 直接给出
}

```

## 反射机制

java.reflection

#### 反射概述



```java
function f(){
    var x = "var a=3;var b=5;alert(a+b)"
}
Reflection（反射）是java被视为动态语言的关键，反射机制允许程序在执行期借助于Reflection API
取德任何类的内部信息，并能直接操作任意对象的内部属性和方法。

Class c = Class.forName("java.lang.String")
加载完类后,在堆内存的方法区中生成了一个Class类型的对象（一个类只有一个Class对象），该对象包含完整类的结构信息。其中，可以通过该对象看到类的结构。所以，Java反射就是 Java能通过对象看到类结构的能力。
Normal: 引入需要的“包类”名称 -> 通过new 实例化 在堆内存中创建 -> 取得实例化对象
reflection: 实例化对象 -> getClass()方法 -> 得到完整的“包类”名称
```

```java
//什么叫反射
public class Test02{
    main方法 throws ClassNotFoundException{
       // get Class object by reflection
        Class c1 = Class.forName("com.kuang.reflection.User");
        Class c2 = Class.forName("com.kuang.reflection.User");
        Class c3 = Class.forName("com.kuang.reflection.User");
        System.out.print(c1);	//output:class com.kuang.reflection.User
        c1.hashcode() = c2.hashcode() = c3.hashcode();//一个类在内存中只有一个class对象 一个类加载后，类的整个结构都会被封装在Class对象中。
            
    }
}
//实体类
class User{
	private String name;
    private int id;
    private int age;
}
public String getName(){
    return name;
}
public void setName(String name){
    this.name= name;
}//类似这种 get set方法 还有 Id Age 
//自动生成toString()
@Override
public String toString(){
    return "User{"+
        "name='"+name+'\''+
        ",id="+id+
        ",age="+age+
        '}';
}
```

![image-20220325143408005](C:\Users\Dou\AppData\Roaming\Typora\typora-user-images\image-20220325143408005.png)

Class本身也是一个类

Class对象只能由系统建立对象

一个加载的类在JVM中只会有一个Class实例

一个Class对象对应的是一个加载到JVM中的一个class文件

每个类的实例都记得自己是由哪个Class实例所生成

通过CLass可以完整地得到一个类中的所有被加载的结构

class类是Reflection的根源，针对任何想动态加载、运行的类，唯有先获得的相应的Class对象

#### How to get Class's instance?

```java
1. Class clazz = Person.class;  //已知具体类，通过类的class属性获取，安全可靠，程序性能最高
2. Class clazz = person.getClass();	// 已知某个类的实例，调用该实例的getClass()方法获取Class对象
3. Class clazz = Class.forName("com.xxx.xxx.user"); //已知全名，通过Class类的静态方法forName()获取。
public class Test03{
    main方法{
        Person person  = new Student();
        print("这个人是："+person.name);
        //1
        Class c1 = person.getClass();
        print(c1.hashcode());
        //2
        Class aClass = Class.forName("com.xxx.");
        print(c2.hashcode());
        //3
        Class c3 = Student.class;
        print(c3.hashcode());
        // 基本内置类型的包装类都有一个Type属性
        Class c4 = Integer.Type;		// c4: int
        //获得父类类型
        Class c5 = c1.getSuperClass();	//c5: class com.xxx.xxxx.Person
        
        
	}
}

class Person{

}
class Student extends Person{
    public Student(){
        this.name = "学生";
    }
}
class Teacher extends Person{
    public Teacher(){
        this.name = "老师";
    }
}

```

```java
//所有类型的Class
public class Test04{
    main(){
        Class c1 = Object.class;
        Class c2 = Comparable.class;
        Class c3 = String[].class;//1维数组
        Class c4 = int[][].class;//2 - dim array
        Class c5 = Override.class;
        Class c6 = ElementType.class;
        Class c7 = Integer.class;
        Class c6 = void.class;
        Class c6 = Class.class;
     	int a = new int[10];
        int b = new int[1000];
        a.getClass().hashcode();
        b.getClass().hashcode();
    }
}
```

<img src="C:\Users\Dou\AppData\Roaming\Typora\typora-user-images\image-20220331152901078.png" alt="image-20220331152901078" style="zoom: 67%;" />



一个类只有一个Class对象 相对应的hashcode()也一样



## 类的加载过程

![image-20220331153344440](C:\Users\Dou\AppData\Roaming\Typora\typora-user-images\image-20220331153344440.png)

![image-20220331153455901](C:\Users\Dou\AppData\Roaming\Typora\typora-user-images\image-20220331153455901.png)

![image-20220331154325174](C:\Users\Dou\AppData\Roaming\Typora\typora-user-images\image-20220331154325174.png)

```java
public class T05{
    main{
        A a = new A();
        print(A.m);//先等于了300 然后又等于了100
        
        1.加载到内存，会产生一个类对应Class对象;
        2.链接，链接结束后m=0;
        3.初始化<clint>
         
    }
}

Class A {
    
    //静态代码块
    static {
        System.out.print("静态");
         
    }
    //初始化 静态的都合并
    static int = 100;
    
    public A(){
        print("无参构造");
    }
}
```

![image-20220331155150977](C:\Users\Dou\AppData\Roaming\Typora\typora-user-images\image-20220331155150977.png)



![image-20220331161443113](C:\Users\Dou\AppData\Roaming\Typora\typora-user-images\image-20220331161443113.png)





![image-20220331161711418](C:\Users\Dou\AppData\Roaming\Typora\typora-user-images\image-20220331161711418.png)

测试类什么时候被初始化



```java
class test06{
    static {
        print("Main加载");
    }
    main{
    //1.主动引用
    Son son = new Son();
    //2.反射也会产生主动引用
    Class.forName("com.kuang.reflection.Son");
        
        
    //3.不会产生类的引用的方法
    print(Son.b);
        
    //4.
    Son[] array= new Son[5];
    //5.
    print(Son.M)
	}
}

public class Father{
    static int b = 2;
    static {
        print("父类加载")
    }
}

class Son extends Father{
    static{
        print("子类加载");
    }
    Static int m = 100;
    static final int M = 1;
    
}
```

方法1的运行结果

![image-20220331162149779](C:\Users\Dou\AppData\Roaming\Typora\typora-user-images\image-20220331162149779.png)

方法2的运行结果

![image-20220331162618971](C:\Users\Dou\AppData\Roaming\Typora\typora-user-images\image-20220331162618971.png)

反射也会加载所有的东西，消耗资源



方法3的运行结果 子类调用父类的静态方法 不会引起子类的加载

![image-20220331163155238](C:\Users\Dou\AppData\Roaming\Typora\typora-user-images\image-20220331163155238.png)



方法4

![image-20220331163342505](C:\Users\Dou\AppData\Roaming\Typora\typora-user-images\image-20220331163342505.png)



方法5  常量在链接阶段就完成初始化了，进入了常量池中了

![image-20220331163856381](C:\Users\Dou\AppData\Roaming\Typora\typora-user-images\image-20220331163856381.png)





### 类加载器的作用

![image-20220331164326257](C:\Users\Dou\AppData\Roaming\Typora\typora-user-images\image-20220331164326257.png)

![image-20220331164803904](C:\Users\Dou\AppData\Roaming\Typora\typora-user-images\image-20220331164803904.png)





```java
class Test07{
    main{
        //获取系统类的加载器
        ClassLoader systemClassLoader = ClassLoader.getSystemClassLoader();
        print(systemClassLoader);
        //获取系统类加载器的父类加载器->扩展类加载器
        ClassLoader parent  =  systemClassLoader.getParent();
        print(parent);
        
        //获取扩展类加载器的父类加载器->根加载器
        parent1 = parent.getParent();
        print(parent1);
        
        //测试当前类是哪个加载器加载的
        ClassLoader classloader = Class.forName("com.kuang.reflection.Test07").getClassLoader();
        print(classloader)
        //测试JDK内置类是谁加载的
        classloader
        
    }
}
```

