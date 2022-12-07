Java运行机制

编译型 将代码编译成计算机可读代码

解释型 例如HTML  写好的代码就可以被浏览器解释



.java   ->   Java编译器  -> .class  -> JVM虚拟机｛类加载器 -> 字节码校验（检查代码写的对不对）-> 解释器->操作系统平台｝

## Java 基础语法

#### 标识符

所有字符都应该是  A-Z  a-z  $  _ 开始

#### 数据类型

强类型语言：所有变量必须先定义后才能使用

弱类型语言

Java数据类型分为两大类：

基本类型primitive type

引用类型reference type

![image-20210918191137417](C:\Users\Dou\AppData\Roaming\Typora\typora-user-images\image-20210918191137417.png)



二进制0b  十进制  八进制0 十六进制0x

```java
int i = 10;
int i2 = 010;	//八进制
int i3 = 0x10;	//十六进制
10 8 16

    
//https://blog.csdn.net/anxia3746/article/details/101454062
float	有限 离散 摄入误差
double	
//最好完全避免使用浮点数进行比较而是使用BigDecimal (银行)
float f = 0.1f;
double d = 1.0/10;
System.out.print(f==d); // false
float d1 
float 不能用==,>=,<=;
要是比较两个浮点数是否相等则
const float EPSINON = 0.00001;
if(abs(a-b)<=EPSINON)
//程序中应尽量避免浮点数的比较
//float、double类型的运算往往都不准确
//计算机中float 与double 转二进制后的存储都是不准确的
    
    
char c1='a';	//97 强转数字后
char c2='中';	//20013
//unicode 2字节 2^16   0-65536

char c3='\u0061';
System.out.print(c3);	//a

//转义字符
System.out.print("Hello\nWorld");

//布尔值扩展
boolean flag =true;
if(flag==true){}	//新手
if(flag){}	//老手
//less is More

```

#### 类型转换

小数优先级大于整数

```java
int i = 128;
```

#### 用户交互使用Scanner

```java
Scanner scanner = new Scanner(System.in);
System.out.println("请输入数据：");
String str = scanner.nextLine();
System.out.println("输出的内容为："+str);
scanner.close();
//->System.out.println(""+(a+b)); ->if a = 1 b= 2 ->(String) 12
//->System.out.println((a+b)+""); ->if a = 1 b= 2 ->(String) 3

```





#### 选择语句

```java
//JDK7 之后的新特性，表达式结果可以是字符串
//字符的本质还是数字
//反编译   java  --- class (字节码文件)   

//还有case穿透现象
switch(name){
    case "xx":
        System.out.print("xx");
        break;
    case "ss":
        System.out.print("ss");
        break;
    default:
        System.out.print("!!");
}


```

![image-20210922152440649](C:\Users\Dou\AppData\Roaming\Typora\typora-user-images\image-20210922152440649.png)



#### For循环

```java
for(int i=0;i<100;i++)
//执行步骤
第一步先运行初始化语句
第二步检测布尔表达式 true->执行循环体  false->循环终止
第三步更新循环控制变量
第四步检测布尔表达式
........
```





#### 异常处理面试





#### 集合总结

Map接口和Collection接口是所有集合框架的父接口

Collection: LinkList   ArrayList HashSet TreeSet

Map: HashTable HashMap TreeMap

List有序集合 Set无序集合(不可存储重复元素，只能存一个null) Map键值对方式存取

List: ArrayList 数组    LinkedList 双向链表

Set：HashSet基于HashMap实现    TreeSet（红黑树）

Map：HashMap JDK1.8之前 数组+链表  1.8之后数组+链表/红黑树 界限为8

HashTable 数组+链表 安全

TreeMap 红黑树

##### Array 和 ArrayList区别

Array大小固定 ArrayList是动态变化的  

ArrayList扩容：初始容量为10，旧的容量值加上就得容量数值进行右移一位，所以是1.5倍

##### ArrayList与LinkedList的区别

都不安全

一个数组，一个链表

插入删除，随机读取各自有缺分析

内存占用：每次扩容为之前1.5倍，而LL就插入一个数据

##### HashSet实现原理

底层HashMap，默认容量16，负载因子0.75，HashSet值是存放在Key上来实现。

HashSet为了能够保证数据不可重复（若每个Key算出来的hash 使用equals比较 若是根据地址计算出的hashcode那每个都不一样，可能会重复，所以需要使用String类中的equals方法，地址不一样没有关系，进一步比较对象类型和数值，对象类型和值都匹配就返回True，以此保证数据不可重复）

##### HashMap的扩容如何实现

初始16 16*0.75=12 即为阈值

若大于12调用resize()方法扩容

2倍扩容

##### HashMap如何解决哈希冲突的

计算的Hash值最后都会根据数组长度取余，总会存在冲突。

1.拉链，就是拉个链表，将Key对应的Value尾插在后面 大于8转红黑

## Java如何读取一个.txt文件呢？

```java
//pvsm创建main主体
try{
	String path = "D:"+File.separator+"OMG"+File.separator+"Record.txt";
	File file = new File(path);
	if(file.isFile()&&file.exists()){
        InputStreamReader read = new InputStreamReader(new FileInputStream(file),"GBK");
        
        BufferedReader bufferedReader = new BufferedReader(read);
        
        String lineTxt = null;
        
        while((lineTxt = bufferedReader.readLine()))
        	System.out.print(lineTxt);
        
        read.close();
	}else{
        System.out.print("找不到指定文件");
    }
}catch(Exception e){
    System.out.print("读取文件内容出错");
    e.printStackTrace();
}

```

##### 抽象类





string的equals

这与Object方法中equals不同，有被重写过。
因为object的equals方法是比较的对象的内存地址，而String的equals方法比较的是对象的值。

```java

public boolean equals(Object anObject) {
	//如果指定对象和当前对象的内存地址相等，则啥都不用说了，在内存中都是同一个对象了，直接返回true；
	if (this == anObject) {
		return true;
	}
	//如果指定对象和当前对象的内存地址不相等：
	//则判断指定对象是否是String类型，如果不是，则直接返回false；
	//如果指定对象是String类型，则继续：
	if (anObject instanceof String) {
		//则先将该对象强转为String对象：
		String anotherString = (String)anObject;
		//比较指定对象与当前对象的字符串长度，如果长度不一致，则直接返回false；
		int n = value.length;		
		if (n == anotherString.value.length) {
			//如果指定对象与当前对象的字符串长度一致，
			//则遍历这两个字符串对象中的每一个char字符，
			//如果遍历过程中有一个char字符不一致，则直接返回false；
			char v1[] = value;
			char v2[] = anotherString.value;
			int i = 0;
			while (n-- != 0) {
				if (v1[i] != v2[i])
					return false;
				i++;
			}
			//如果遍历结束，每个char字符都一致，则返回true；
			return true;
		}
	}
	return false;
}

```

所以，若是HashMap存储非String类为对象的话，equals比较的是内存地址。所以，如果出现业务逻辑，如具有相同ID的Student pojo，在数据库中那必然一个用户，如果s1与s2都是id=1的pojo类。那么存储时，hashcode必须要一样，所以要改成使用id.hashcode()，而不是对内存.hashcode(这必然会不一致)。此外equals也要重写，因为他调用的是Object的equals，比较的是地址，两者必然是false，所以，要比较他的key，与String一样，这样就能保证不一样的两个实体但有一样的id 是一个东西。
