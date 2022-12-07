# CSS预处理器

SASS：基于Ruby，服务端处理，功能强大，解析效率高

LESS：基于NodeJS，通过客户端处理，使用简单。解析效率低于SASS

# 行为层JavaScript

JS：一门弱类型脚本语言，其源代码在发往客户端运行之前不需经过编译。



## ECMAScript

因为网景开发了JavaScript，一年后微软又模仿JavaScript开发了JScript，为了让JavaScript成为全球标 准，几个公司联合ECMA（European Computer Manufacturers Association）组织定制了JavaScript语 言的标准，被称为ECMAScript标准。 所以简单说来就是，ECMAScript是一种语言标准，而JavaScript是网景公司对ECMAScript标准的一种实 现。 那为什么不直接把JavaScript定为标准呢？因为JavaScript是网景的注册商标。 不过大多数时候，我们还是用JavaScript这个词。如果你遇到ECMAScript这个词，简单把它替换为 JavaScript就行了。

## UI框架：

Ant-Design：基于React

ElementUI，iview，ice：饿了么出品，基于Vue的UI框架

Bootstrap：Twitter出品

AmazeUI：又称“妹子UI”，HTML5跨屏前端框架



## JS构建工具

Babel：JS编译工具。主要用于浏览器不支持的ES新特性

WebPack：模块打包器，主要作用是打包、压缩、合并以及按序加载

## 三端统一

混合开发 HybridApp

主要目的是实现一套代码三端统一（PC，Android，IOS）并能够调用到设备底层硬件（传感器，GPS，摄像头等），打包方式主要有以下两种：

云打包：HBuild->HBuildX,DCloud APICloud

本地打包：Cordova（前身是PhoneGap）

## 后端技术

为了方便前端人员开发后台应用，出现了NodeJS技术。

NodeJS作者开始开发全新架构Deno

NodeJS框架及项目管理工具如下：

​	Express：NodeJS框架

​	Koa：Express简化版

​	NPM：项目综合管理工具，类似maven

​	YARN：NPM代替方法，类似MAVEN和GRADLE的1关系

## 前端主流框架-Vue.JS

# 1.什么是JS

最流行的脚本语言

大部分浏览器还停留在支持es5上

# 2.快速入门



## 2.1基本语法入门

```javascript
// 变量类型 变量名 = 变量值;
var num = 1;
var name = "qingjiang"; 
alert(name); 弹窗
```

## 2.3数据类型

数值、文本、图形、音频、视频

js不区分小数和整数，Number

```javascript
变量命名
var $1 // $,_都可以开头命名
全局变量:
1. 在函数外 使用var
var name ="1";
function xx(){
    
}
2. 不使用var
name = "1";
3. window.name="1";

局部变量：
不带var命名，将会得到
var/let xxx = "xxx";是局部变量  //ES6是使用let定义局部变量
var 是函数作用域  也就说var在某个函数内总是有效的
let 是块作用域	let则会被限制在某个代码块内有效

如何进行严格检查呢，加入一行
'use strict';  //严格检查模式，局方js随意性产生的问题





123 //整数123
123.1  // 浮点数123.1
1.123e3 // 科学计数法
-99 //复数
NaN // 未定义或不可表示的值
Infinity //无穷的

字符串
'abc' "abc"
布尔值

比较运算符
=
==
=== 绝对等于(类型一样，值一样结果为true，否则为false)
NaN===NaN,这个与所有的数值都不想等，包括自己
只能通过isNaN(NaN)来判断这个数是否是NaN


浮点数问题：
console.log((1/3)==(1-2/3))
尽量避免使用浮点数进行运算，存在精度问题
Math.abs(((1/3)-(1-2/3))<0.00000001)  true
null 和undefined
null 空
undefined 未定义

数组
Java数组一些列相同类型的对象，JS中可不一样
var arr=[1,2,3,4,5,'hello',null,true] // 保证代码可读性，尽量保证使用[]
new Array(1,2,2,3,4);

取数组下标，如果越界，就会得到undefined
对象是大括号，数组是中括号
var person = {
    name:"renwei",
    age:3,
    tags:['js','java','web','....']
}


```

## 2.4、严格检查格式



# 3、数据类型

## 3.1字符串

1.正常字符串使用单引号或双引号

2.转移字符   \

3.js特有的长字符串  票号

```javascript
'use strict'
var msg=`hello
world
wss`
```

4.模板字符串

```javascript
let name = "rw";
let age = 3;
let msg = `你好呀,${name}`
```

5.字符串长度

```javascript
var stu="student";
console.log(str.length);
```

6.字符串的可变性，java中不可变,因为String  StringBuilder底层哦都市final修饰，不可变的，StringBuffer是可变类

1. **String保存的是字符串常量**，里面的值不能更改，每次String类的更新实际上是更改地址，效率比较低
   - `private final char value[];`，放在常量池中
2. **StringBuffer保存的是字符串变量**，里面的值可以更改，每次更新实际上可以更细内容，不用每次更新地址，效率比较高

js也一样

student[0]=1;

console(student);并未改变

https://www.shuzhiduo.com/A/pRdBWwP6zn/



7.大小写转换

```javascript
student.toUpperCase();
student.toLowerCase();
student.substring(1,3)
student.indexOf('t')
```

## 3.2数组(存储数据)

Array可以包含任意的数据类型



```javascript
1.长度
可以给arr.length 赋值
数组大小会发生变化，过小，元素就会丢失。过大就会多出undefined
2.indexOf 通过元素获得下标索引
arr.indexOf(2)
3.slice() 数组版的SubString,截取Array的一部分，返回一个新的数组
arr.slice(3)  .slice(1,5) 包头不包尾
4.push,pop 
arr.push('a','b');尾部插入
arr.pop();弹出尾部一个元素
5.unshift(),shift()头部插入
arr.unshift('a','b');
arr.shift() 弹出头部一个元素
6.sort()
arr.sort()
7.reverse()
arr.reverse()
8.concat()
9.连接符join
打印拼接数组，使用特定的字符串连接
arr.join('-')
"C-B-A"
10.多维数组
arr=[[1,2],[3,4],["",""]]
arr[1][1]= 4


```

## 3.3对象

```javascript
var 对象名={
	属性名: 属性值,
	属性名: 属性值,
	属性名: 属性值
}
var person={
    name:"kuangshen",
    age:3,
    email:"111@qq.com",
    score:0
}
1.使用一个不存在的属性不会报错!undeined
2.动态删减属性
delete person.name
ture
person
3.动态的添加
person.haha="haha"
person
发现增加了新的属性
4.判断属性值是否在这个对象中！xxx in xxx
person['age']
得到应当的值
"age" in person?
true
//in 还能找到父类的方法
'toString' in person?
true

5.判断一个属性是否是这个对象自身拥有的hasOwnProperty()
person
```

## 3.4流程控制

与java的区别

```javascript
var age=[12,3,12,3,12,3,12,31,23,123];
age.forEach(function(value){
    console.log(value);
});
a.forEach(value => console.log(value));

for(var num in age){
    console.log(num);  //得到的都是key对象,也就是索引
    console.log(age[num]);
}
//es6中 使用of可以直接打印结果，map set一样可以
for(var num of age){
    console.log(num);  //得到的都是key对象,也就是索引
}


```

## 3.5 Map和Set

```javascript
java中HashMap和Map的区别是，Map的key不能重复，否则会覆盖。而HashMap采用链地址发key可以重复，后以尾插或者头插的方式存储数据
//ES6新特性
new Map();
new Set();
//学生的成绩和学生的名字
var map =new Map([["tom",100],["jack",90]]); //正常应该是一个唯一id做key合适
var name = map.get('tom');
map.set('admin',123456);

//无序不重复集合
var set = new Set([3,1,1,1,1]); //set可以去重
set.add(2);
[3,1,2]
set.delete(1);
[3]
console.log(set.has(3)); // true

```

## 3.6 iterator

使用iterator遍历迭代map，set。

```js
forEach 是5.1特性
iterator 是ES6特性
'use strict';
// 遍历set
var set = new Set([5,6,7]);
for(let x of set){
    console.log(x)
}

// 遍历map
var map = new Map([["tom",100],["jack",90]])
for(let x of map){
    console.log(x);
}

```



# 4.函数及面向对象

方法：对象{

属性;

方法;

}

函数：

4.1 函数定义和变量作用域

```js
public 返回值类型 方法名(){
    return 返回值;
}

function abs(x){
    if(x>=0){
        return x;
    }else{
        return -x;
    }
} 
如果没有执行return 函数执行完也会有返回值，返回undefined

方式2
var abs=function(x){
    //如果参数类型不等于数字,手动抛出异常
	if(typeof x!=='number'){
        throw 'Not a Number'
    }
    // arguments表示传入的参数组
    // 问题：arguments包含所有的参数，我们有时候就只是用多余的参数
    for(var i=0;i<arguments.length;i++){
        console.log(arguments[i]);
    }
}
js可以任意个参数，也可以不传
arguments 是一个ja免费赠送的一个关键字
//es6新特性
rest
function aaa(a,b){
    console.log(a);
    console.log(b);
    console.log(rest); //rest可以输出剩下的参数
    //比如输入(1,2,3333,4)
    // 打印
    1
    2
    [2] 3333,4
}

//变量作用域
1.假设在函数体内声明，则在函数外不能使用
2.如果想用函数内的变量，则需要闭包
3.各个函数内变量名相同不冲突
4.嵌套式函数，里层可以用外层，外层不能用里层
5.内外部var声明的变量重名，只用块内的变量，由内向外查找变量(let将会变化)
假设外部存在同名函数变量，则内部屏蔽外部
function qj(){
    var x= '1';
    function qj2(){
        var x = 'A';
        sout(x);  //输出A 我认为这是因为地址不一样导致的
    }
    sout(x);// 输出1
}

//全局变量
<script>
    var x='xxx';
	与
    window.x等价
</script>

alert也是window中的方法，全局方法
window.alert=function(){
    
}; 全局方法被覆盖

由于所有的全局变量都会绑定到window上。如果不同的js文件，使用了相同的全局变量，冲突～>
    如何才能减少冲突？
var kuangAPP={};
kuangAPP.name='kuangshen';
kuangAPP.add=function(a,b){
    return a+b;
}
把自己的代码放入自己定义的唯一空间名字中，降低全局名冲突的问题。

function aaa(){
    for (var i =0;i<10;i++){
        console.log(i);
    }
    console.log(i+1);
}
会得到1-11结果，显然，for块内的变量在块外依旧被使用

ES6 let关键字来解决局部作用域冲突的问题。

因为很多时候全局变量能被改变是不能忍受的，ES6引入了常量关键字const
const PI= '3.14';

```

4.2方法

```js
//方法就是吧函数放在对象内部,对象只有两个东西：属性和方法
function getAge(){
    var now=new Date().getFullYear();
    return now- this.birth;
}
var kuangshen = {
    name: 'rw',
    birth:2020,
    //方法
    // 今年-出生的年
    age: function(){
        var now=new Date().getFullYear();
        return now- this.birth;
    }
    或者
    age: getAge
}
//方法调用
kuangshen.age()

this 是无法指向的，默认指向调用它的对象,但在js中可以控制js指向---apply
getAge.apply(kuangshen,[]);this指向了kuangshen对象，参数为空
 
1. 如果函数前面没有对象调用this，严格模式下this指向undefined
2. 事件处理函数，自定义对象里面的方法的this指向，根据谁调用函数this指向谁的特性，这里的this都指向当前操作的对象
如某个变量中定义的函数
3.构造函数里面的this指向
    let MyClass = function(){ 
        this.name = 'zhangsan'; 
    }; 
    let obj = new MyClass(); 
    console.log(obj.name); //zhangsan 
    解析：构造函数里的this就指向返回的这个对象
    但用new调用构造函数时，还要注意一个问题，如果构造函数显式地返回了一个 object 类型的对象，那么此次运算结果最终会返回这个对象，而不是我们之前的this。
    let MyClass = function(){ 
        this.name = 'zhangsan'; 
        return { //显式地返回一个对象
            name: 'lisi' 
        } 
    }; 
    let obj = new MyClass(); 
    console.log (obj.name); //lisi 
    如果构造函数不显式地返回任何数据，或者是返回一个非对象类型的数据，就不会造成上述问题，主要是new关键字调用函数，函数内部隐式返回this造成的。
    let MyClass = function(){ 
        this.name = 'zhangsan' 
        return 'lisi';
    }; 
    let obj = new MyClass(); 
    console.log(obj.name); // 输出：zhangsan


4.箭头函数的this指向 ES6新增
对于普通函数来说，内部的this指向函数运行时所在的对象，但是这一点对箭头函数不成立。它没有自己的this对象，内部的this就是定义时上层作用域中(父级作用域)的this。
也就是说，箭头函数内部的this指向是固定的，相比之下，普通函数的this指向是可变的。

https://baijiahao.baidu.com/s?id=1733619328808825470&wfr=spider&for=pc
二.修改this指向的方式
1.利用call,apply,bind方法改变this指向

call方法：
call方法的第一个参数就是新的this指向， 从第二个参数开始表示函数自身的参数。
const obj = {
    a: 100,
};

function sum(x, y) {
 console.log(this.a + x + y);
}
sum(3,7);   //undefined+ 3 + 7 = NaN
//直接调用，this指向window，window下面没有a属性，所以window.a是undefined
sum.call(obj, 3, 7); //100 + 3 + 7 = 110
// 通过call改变this，让其指向obj，obj下面具有a属性

apply方法：
apply方法的第一个参数就是新的this指向， 第二个参数是一个数组或者类数组，里面的值依然是函数自身的参数。
sum.apply(obj, [3, 7]); //100 + 3 + 7 = 110 注意中括号是apply的第二个参数必须是数组或者类数组。
解析：通过apply改变this，让其指向obj，obj下面具有a属性，所以obj.a是100

bind方法：
bind方法的第一个参数就是新的this指向， 从第二个参数开始表示函数自身的参数,但bind 是返回对应函数体，便于稍后调用，apply、call则是立即调用

sum.bind(obj, 3, 7)(); //100 + 3 + 7 = 110  注意这里需要再次调用
解析：通过bind改变this，让其指向obj，obj下面具有a属性，所以obj.a是100

bind的使用场景
案例：将fn函数内部的this指向obj对象，2s后输出this.num的值。
window.num = 100; //window添加属性
const obj = {
  num: 1000
}

function fn() {
 console.log(this.num);
}

fn(); //100 这里的this指向window
window.setInterval(fn.call(obj), 2000); //此时的定时器无效，已经调用fn函数，
window.setInterval(fn.apply(obj), 2000); //此时的定时器无效，已经调用fn函数
window.setInterval(fn.bind(obj), 2000); //定时器的第一个参数是函数体或者函数名称，bind返回的就是函数体，所以吻合需求。

三、将正确的this存储
通过变量将需要的this存储下来，然后在函数内部利用存储的this达到我们的目标。


var name = "The Window";
var object = {
    name : "My Object",
    getNameFunc : function(){
        return function(){
         return this.name;
        };
    }
};
alert(object.getNameFunc()());//"The Window"
解析：
var声明的变量是window下面的属性
object对象里面存在方法getNameFunc，而且这个方法的返回值也是一个函数，必须再次调用。
通过返回的函数里面存在this，但是这里的this不是通过object进行调用，因为object.getNameFunc()返回的是一个函数体(普通的函数表达式)，需要对函数体进行再次调用，而再次调用就和object对象没有关系了，所以这里的this不会指向object,而是指向window,结果输出"The Window"

var name = "The Window";
var object = {
    name : "My Object",
    getNameFunc : function(){
        var _this = this;
        return function(){
         return _this.name;
        };
    }
};

alert(object.getNameFunc()()); //"My Object"
解析：
这里大部分情况和上面的一致，区别是object.getNameFunc方法里面提前将指向object对象的this存储为变量，并且在返回函数体的时候使用了这个变量，所以返回的函数中的this就是object.getNameFunc方法里面的this，即指向了object对象，结果输出 "My Object"
```

4.3闭包( closure)

https://blog.csdn.net/weixin_58830590/article/details/125208445

**闭包的作用**:通过一系方法,将函数内部的变量(局部变量)转化为全局变量。

变量的作用域无非就是两种：**全局变量和局部变量**。

Javascript语言的特殊之处，就在于函数内部**可以直接读取全局变量**。

另一方面，在**函数外部无法读取函数内的局部变量**。

```js
这里有一个地方需要注意，函数内部声明变量的时候，一定要使用var命令。如果不用的话，你实际上声明了一个全局变量！

function f1(){
    n=999;
}
f1();
alert(n);   // 999

```

二、如何从外部读取局部变量?

```js
出于种种原因，我们有时候需要得到函数内的局部变量。
正常情况下,父对象的所有变量，对子对象都是可见的，反之则不成立。

function f1(){
    var n=999;   
　　function f2(){
　　    alert(n); // 999
　　}
}
既然f2可以读取f1中的局部变量，那么只要把f2作为返回值，我们不就可以在f1外部读取它的内部变量了吗！
function f1(){
　    var n=999;
　　　function f2(){
　　      alert(n); 
　　　}
　　　return  f2;
　}
　var result=f1();
　result(); // 999

```

各种专业文献上的"闭包"（closure）定义非常抽象，很难看懂。
我的理解是，**闭包就是能够读取其他函数内部变量的函数**。
所以，在本质上，闭包就是将函数内部和函数外部连接起来的一座桥梁。

三、闭包的用途

闭包可以用在许多地方。它的最大用处有两个，一个是**前面提到的可以读取函数内部的变量**，另一个**就是让这些变量的值始终保持在内存中**。

```js
function f1(){
    var n=999;
    nAdd=function(){
        alert(n+=1);  
    }
    function f2(){
        alert(n);
    }
    return f2;
}
var result=f1();
result(); //999
nAdd();//1000
result(); //1000


```

为什么会这样呢？原因就在于f1是f2的父函数，而f2被赋给了一个全局变量，这导致f2始终在内存中，而f2的存在依赖于f1，因此f1也始终在内存中，不会在调用结束后，**被垃圾回收机制（garbage collection）回收。**

这段代码中另一个值得注意的地方，就是"nAdd=function(){n+=1}"这一行，首先在nAdd前面没有使用var关键字，因此nAdd是一个全局变量，而不是局部变量。其次，nAdd的值是一个匿名函数（anonymous function），**而这个匿名函数本身也是一个闭包**，**所以nAdd相当于是一个操控者**，可以在函数外部对函数内部的局部变量进行操作。

四、使用闭包的注意点

1.由于闭包会使得函数中的变量都被保存在内存中，**内存消耗很大**，所以**不能滥用闭包**，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是**，在退出函数之前，将不使用的局部变量全部删除。**

2.

4.4箭头函数（新特性）

4.5创建对象

4.6class继承（新特性）

# 5.常用对象

5.1 内部对象

```js
typeof []       "object"
typeof NaN     "number"
typeof Math.abs    "function"
typeof undefined "undefined"


```

5.2 Date

```js
var now = new Date();
now.getTime(); //时间戳
1578106175991

console.log(new Date(1578106175991))//时间戳转 标准时间

now.toLocaleString();
now.toGMTString();


```

5.3 JSON

JSON是一种轻量级的数据交换格式，基于ECMAScript的一个子集，采用完全独立于编程语言的文本格式来存储和表示数据。也易于机器解析和生成

早期，所有数据传输习惯使用XML

```js
格式：
对象用{}
数组用[]
所有的键值对都是用key:value

var user = {
	name: "rw",
    age: 3,
    gender: '男'
}
// 对象转化为json字符串
var json = JSON.stringify(user);
sout(json) //{"name":"rw","age":3,"sex":"男"}
// json字符串 转化为对象 里双外单或 里单外双
var obj = JSON.parse('{"name":"rw","age":3,"sex":"男"}');

```

JSON和JS对象的区别

```js
var obj={a: "hellow", b: "he"};
var json={"a",'he',"b",'sss'};

```

5.4 Ajax

原生的js写法，xhr异步请求



```js
jQuery 封装好的方法  $('#name').ajax("")
axios请求
```

java中

类是模板

对象是具体事例

js中

```js
var user ={
    name: "rew",
    age: 3,
    run: function(){
        console.log(this.name+"run....");
    }
};
var xiaoming={
    name: "rewwerw"
};
//xiaoming.run失败
xiaoming.__proto__ = user
//xiaoming.run成功
// xiaoming继承了user
function student(name){
    this.name= name;
}
//ES6之前，若要给student新增方法
student.prototype.hello =  function(){
    
}

//ES6之后
1.定义一个类、属性和方法
class Student{
    constructor(name){
        this.name = name;
    }
    hello(){
        alert('hello');
    }
}
var xiaoming = new Student("xiaoming");

class XiaoStudent extends Student{
    constructor(name,grade){
        super(name)
        this.grade = grade;
    }
    
}
2.本质
查看对象原型

3.原型链，环形，始终指向object

```

![image-20221105000255559](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221105000255559.png)

# 6.操作DOM元素

DOM：文档对象模型，document object model

```js

<div id="father">
    
</div>    

<script>
	var p1=document.getElementById("p1");
	var h1=document.getElementByTagName("h1");
	var h1=document.getElementByClassName("p2");
	var father = document.getElementById('father');

var childrens = father.children;
father.firstChild
father.lastChild
获得dom节点
</script>
以上是原生代码



之后尽量使用JQuery();
<div id="id1"></div>
var id1=document.getElementById("id1");
id1.innerText='123';//他不能解析标签
id1.innerHTML="<strong>123</strong>";//可以解析
id1.style.color = red;
id1.style.fontSize = '20px'
id1.style.padding = "2em"
var ss = document.getElementById('su');


删除节点
1.先获取父节点，再通过父节点删除自己。
2.
<div id='father'>
    <p id='p1'></p>
</div>
<script>
    var p1=document.getElementById('p1');
	var father = p1.parentElement;
	father.removeChild(p1);
	father.removeChild(father.children[0])
	father.removeChild(father.children[1])
	father.removeChild(father.children[2])
	//由于动态删除，删除0后，原来的1变成了0，所以删除节点时，都是在不断更新的
	
	// 插入节点
	1.获得某个dom节点。若它的节点内为空，就可以通过innerHTML增加元素。若不为空，直接使用innerHTML将会产生覆盖。
	如图1
    var js = document.getElementById('js');
	var list = document.getElementById('list');
	//	把js追加到这里
	list.appendChild(js);
	
	//另一种追加，首先创建一个新的节点
	var newp=document.createElement('p');
	newp.id='newp';
	newp.innerText = 'hello';
	newp.style.
	list.appendChild(newp);
	//当然也能创建一个script脚本节点
	var myScript= document.createElement('script');
	myScript.setAttribute('type','text/javascript');
	
	//body也可以更改
	var body= document.getElementByTagName('body');
	body[0].style.background='#a9475b'
	body[0].setAttribute('style','background-color: wheat')
	body是一个数组
    
    //或者创建css样式
    var myStyle = document.createElement('style');
	myStyle.setAttribute('type','text/css');
	myStyle.innerHTML = 'body{background-color: chartreuse}';
	document.getElementByTagName('head')[0].appendChild(myStyle);
	
	//insert
	var ee =document.getElementById('ee');
	var jj = document.getElementById('jj');
	var list = document.getElementById('list');
	// 要包含的节点，inserBefore(newNode,targetNode)
	list.insertBefore(js,ee);
	// https://blog.csdn.net/hjc256/article/details/89117430
</script>


```

![image-20221105231533764](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221105231533764.png)

图1

# 7.操作Bom元素

BOM: 浏览器对象模型

**浏览器内核**

IE 6-11  chrome  safari firefox opera

**三方浏览器**

QQ浏览器  360浏览器



```js
window.alert(1)
window.innerHeight
window.outerHeight

Navigator封装了浏览器的信息
navigator.appVersion
navigator.userAgent
navigator.platform //"win32"

大多数时候，我们不会使用navigator对戏那个，因为会被人为修改。


screen 可以控制屏幕
//屏幕尺寸
screen.width  //1920px
screen.height //1080px
```



location * 代表当前页面URL信息
前台输入

![image-20221105121058819](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221105121058819.png)

```js
host:"www.baidu.com"
href:"https://www.baidu.com/"
protocol: "https:"
reload:f reload()  // location.reload() 刷新网页
//设置新的地址
location.assign('https://blog.kuangstudy.com/') //进百度里，设置js代码这一行。
```

document 当前的页面，HTML DOM文档

```js
document.title='狂神说'
<dl id=""></dl>

var dl=document.getElementById('app');

document.cookie //获取cookie，不安全，会被劫持
'BIDUPSID=AC4171A43234742D1945E8AA950F34A2; PSTM=1598375988; ....省略"

劫持cookie原理
www.tabao.com
<script src="aa.js"></script>
<!--恶意人员：获取你的cookie上传到他的服务器-->

服务器端可以设置 cookiehttpOnly
history
浏览器的前进和后退功能就是一段js代码
history.back()//后退
history.forward()//前进

```

# 8.表单验证

JS验证安全不安全
单纯的说这个问题的话，答案是不安全。因为在客户端进行的验证相当于“让用户自己验证自己”，很明显是不靠谱的。你不能避免一些恶意用户人为的修改自己的表单进行欺骗，也不能避免第三方对表单进行截获后进行篡改再提交。
所以说，从安全的角度来说，单纯的依靠js验证，是不安全的，任何健壮的系统都必须在后端进行验证。

但是要说JS验证有没有必要
是非常有必要的，因为首先JS验证速度很快，不需要HTTP交互，可以在本地验证，对于用户来说，提示错误会显得非常迅速。其次是普通用户一般占访问用户的绝大多数，也就是说，大部分的验证工作，都是有效的，在前端验证可以极大的减轻服务器的压力。

所以结论是，不能单单的依靠JS进行表单验证，后端必须也要验证。但是JS验证可以减轻压力、提高用户体验，所以也是必须的





```js
表单的目的：提交信息
下图1为例

//输出目前文本框中的信息
input_text.value
input_text.value = "123"
// 单选
boy_radio.checked;
boy_radio.checked = true;

```

![image-20221106200316301](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221106200316301.png)

提交表单

```js
下图为案例
//md5工具类
<script src="https://cdn.bootcss.com/blueimp-md5/2.10.0/js/md5.min.js"></script>

var uname = document.getElementById('username');
var pwd = document.getElementById('password');
console.log(uname.value);
console.log(pwd.value);
//使用md5算法
pwd.value= md5(pwd.value);
console.log(pwd.value);

或如图2
var uname = document.getElementById('username');
var pwd = document.getElementById('input-password');
var md5pwd = document.getElementById('md5-password');

md5pwd.value = md5(pwd.value);

obsubmit = 绑定一个提交检测的函数，如上面的代码
代码最后可以return true false
onsubmit="return aa()"
不用return
onsubmit="aaaa()"


另一种玩法
//pwd.value = md5(pwd.value)//提交的一瞬间代码变长,所以只需要下面一行就行
md5pwd.value = md5(pwd.value);
```

![image-20221106200706825](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221106200706825.png)

![image-20221106224303932](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221106224303932.png)

# 9.JQuery

js 与jq的关系，jq是个库，里面存在大量的JavaScript函数

## 9.1导入方式

1. 直接下载jq文件，导入项目
2. 通过cdn jquery搜索对应的网址



```Js
公式$(selector).action()

<a href="" id="test-jquery">点我</a>

//id选择器
$('#test-jquery').click(function(){
    alert('hello')
})
//class选择器
$('.class1').click(function(){
    alert('hello')
})
//selector1,selector2,selectorN
$("div,span,p.myClass")
//属性选择   $("div[id]")


```

```js
鼠标事件，键盘事件，

$('.class1').mousedown(function(){})


<style>
    #divMove{
        width:500px;
        height:500px
        border:1px solid red;
    }
</style>

mouse:<span id="mouseMove"></span>
<div id="divMove">
</div>

//当网页元素加载完毕后，响应事件
$(document).ready(function(){
    
});
//可以简化为如下
$(function(){
    //一般用e
    $('divMove').mousemove(function(e){
        $('#mouseMove').text('x:'+e.pageX+'y:'+e.pageY);
    });
});



//操作dom ,例子如下图2
原来使用document.get....
$('#test-ul li[name=python]').text();//获得值
//"Python"
$('#test-ul li[name=python]').text('111'); //设置值

$('#test-ul').html();//get
$('#test-ul').html('<strong>123</storng>');//set


//操作css
$('#test-ul li[name=python]').css("color","red");

//show and hidden
$('#test-ul li[name=python]').show();
$('#test-ul li[name=python]').hide();//本质display= none

$(window).width;$(doc).width;

```

![image-20221106231700942](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221106231700942.png)

![image-20221106233554408](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221106233554408.png)

# 10、Ajax

Ajax？  Asynchronous JavaScript and XML ，一种异步请求方法。

web开发必备

## 10.1案例

Google 的auto_complete(输入框自动补全)

还有百度地图

## 10.2 说明

 浏览器也是支持多线程并发的，其中ajax请求就是一个线程，，一个页面上可以同时发送多个Ajax请求

主要解决的问题：页面局部刷新的问题。

使用Ajax可以在同一个网页中并发的发送多个请求。请求与请求之间互不等待 ，互不干扰。

这样可以提高用的体验。



ajax异步请求用户的体验在浏览器上不存在间断期

但传统请求会清除全部页面。

# 10.3 举例

传统请求。假如有3个请求

请求1

请求2

请求3

点击请求1的时候 会在servlet中停在断点，但是这时候再去请求2时，会刷新页面丢失了请求1。

//（重要）××××××××什么时候使用false，在一个表单当中，多个条目需发送ajax进行验证时，必须等待所有表单验证完毕后，才能允许用户点击注册，此时必须使用同步ajax机制



```js
ajax-get请求
1.注册时，提示该用户名是否被注册
2.文本框失去焦点时，发送AJAX请求，验证该用户名是否可用。可用则显示绿色框，不可用，显示红色框。
onblur="checkUsername(this.value);" //失去焦点


<base href="${pageContext.request.scheme}://${pageContext.request.serverName}:${pageContext.request.serverPort}${pageContext.request.contextPath}">
<script>
    function checkUsername(username){
    	//Ajax发送请求全靠浏览器内置对象：XMLHttpRequest对象。
        //该对象可以在浏览器中单独启动一个新的浏览器线程，并通过该线程发送请求
    	if(window.XMLHttpRequest){
            var xhr = new XMLHttpRequest();
          
        }else{
            //不支持XMLHttpRequest对象，IE5和IE6是不支持的，它只支持ActiveXObject对象
            //也就是说用ActiveXObject发送ajax请求。
            var xhr = new ActiveXObject("Microsoft.XMLHTTP");
        }
    	//2.注册回调函数
    	//等xhr对象的readystate 发生改变的时候，后面的回调函数会执行
		//xhr发送请求后，会根据服务器返回情况 设置readyState为  0:请求未初始化   1:服务器连接已建立 2:请求已接受 3:请求处理中 4:请求已完成，且相应已就绪
    	
    	//下面的回调函数：存储函数，每当readyState属性改变时就会调用onreadystatechange,变一次调用一次
    	xhr.onreadystatechange = function(){
			if(readyState==4){
                if(xhr.status==200){
                    //浏览器xhr对象接收浏览器的responseText
                    var s=xhr.responseText;
                    var nameTipMsg = document.getElementById("nameTipMsg");
                    nameTipMsg.innerHTML=s;
                }else{
                    alert(xhr.status);
                }
            }
        
        }
    
    	
    	//3.开启浏览器和服务器之间的通道
    
    	//method：指定请求方式是get还是post
    	//url ：请求路径
    	// async：true/false（是否支持异步）
    	//xhr.open(method,url,asyn)	
    	// get方法还有一个问题是，当我再次失去焦点 他就不走ajax请求了，走的浏览器缓存
    	// 建议在请求路径中添加时间戳（毫秒）
    	// var timeStamp = new Date().getTime();
    	// xhr.open("GET","checkusername.do?sss="+timeStamp+"&username="+username,true);//开启通道
    	xhr.open("GET","checkusername.do?username="+username,true);//开启通道
    	
    	//4.发送Ajax请求
    	xhr.send();
    	
    
    	//若是表单提交登录数据
    	var nameTipMsg = document.getElementById("nameTipMsg");
    	nameTipMsg.innerHTML = "正在进行身份验证";
	    var username=;
        var pwd=;
        //回调
        xhr.onreadystatechange=function(){
            //若服务器响应结束
            if(xhr.readyState==4){
                if(xhr.status==200){
                    //假设接受服务端响应的JSON数据
                    
                    //接受json　{"success":true} 或者{"success":false}
                    //变成json对象 如果参数是一个表达式，eval() 函数将执行表达式。如果参数是Javascript语句，eval()将执行 Javascript 语句。
                    eval("var jsonObj="+jsonString);
                   
                    if(jsonObj.success){
                        document.location = "list.do";
                    }else{
                        timMsg.innerHTML="error";
                    }

                }else{
                    alert(xhr.status);
                }
            }
        }
    	//（重要）××××××××什么时候使用false，在一个表单当中，多个条目需发送ajax进行验证时，必须等待所有表单验证完毕后，才能允许用户点击注册，此时必须使用同步ajax机制
    	xhr.open("POST","login.do",true);
    	// 模拟表填提交数据
	    // 若ajax post请求乱码，需要注意，客户端和服务端的字符集一致
    	xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded;charset=UTF-8");
    	xhr.send("username="+username+"&password="+pwd);
    
	}
    
    
</script>


ajax发送请求至serlvet，
public class CheckUsernameAction extends HttpServlet{
    doGet{
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = reponse.getWriter();
        
        //1.获取用户名
        String username =request.getParameter("username");
        //2.连接数据库验证用户名是否存在
    	if ("admin".equals(username)){
            //用户名不可用
            out.print("<font color = 'red'>用户名已经被注册</font>");
        }else{
            //用户名可用
            out.print("<font color = 'green'>用户名可用</font>");
        }
    }
}



１）采用绝对路径，但为了解决不同部署方式的差别，在所有非struts标签的路径前加${pageContext.request.contextPath}，如原路径为： 
”/images/title.gif”，改为 
“${pageContext.request.contextPath}/images/title.gif”。 
代码” ${pageContext.request.contextPath}”的作用是取出部署的应用程序名，这样不管如何部署，所用路径都是正确的。
 

缺点： 
操作不便，其他工具无法正确解释${pageContext.request.contextPath} 
２） 采用相对路径，在每个JSP文件中加入base标签，如： 
<base href="http://${header['host']}${pageContext.request.contextPath}/pages/cust/relation.jsp" /> 
这样所有的路径都可以使用相对路径。]

https://blog.csdn.net/sky198989/article/details/84542160
```

```js
若是使用jQuery则可以使用已经封装好的。

```

![image-20221107184209824](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221107184209824.png)



## 10.5 实例

### 问题1 重复请求问题

```js
// get方法还有一个问题是，当我再次失去焦点 他就不走ajax请求了，走的浏览器缓存
// 建议在请求路径中添加时间戳（毫秒）
var timeStamp = new Date().getTime();
xhr.open("GET","checkusername.do?sss="+timeStamp+"&username="+username,true);//开启通道
```



使用全方位ajax完成省市联动效果

```js
需要数据库表

1.在页面加载完毕之后，发送ajax请求，获取所有的省份，
2.后台拼接一个JSON格式的字符串，发送给前端，前端解析json拼接HTML字符串，将HTML字符串设置到下拉列表中
JSON:
[{"code":110,"name":"山东省"},{"code":110,"name":"山东省"},{"code":110,"name":"山东省"}]
前端接受以上的json字符串后，eval()函数生成json对象，循环取出值


<body onload="listProvince();">
    
    <script>
    	function listProvince(){
    		var xhr;
		    if(window.XMLHttpRequest){
                xhr=new XMLHttpRequest();
            }else{
                xhr=new ActiveXObject("Microsoft.XMLHttp");
            }
    		xhr.onreadystatechange = function(){
                if(xhr.readyState==4){
                	if(xhr.status==200){
                        //接受服务器端返回的json
                        var jsonString = xhr.responseText;
                        eval("var jsonObj="+jsonString);
                        
                        //遍历数组
                        var html = "<option value=''>--请选择省份--</option>";
                        for(var i=0;i<arr.length;i++){
                            var provinceJson = arr[i];
                            html+="option value='"provinceJson.code+"'>"+province.name+"</option>";
                        }
                        document.getElementById("provinceList").innerHTML=html;

                    }else{
                        alert(xhr.status);
                    }
                }
            }
    	}
	</script>
</body>
//为什么不用线程安全的StringBuffer
StringBuilder json = new StringBuilder();//线程不安全，效率高
Connection conn= null;
PreparedStatement ps =null;
try catch 操作数据库{
    
    while(rs.next()){
        String code = rs.getString("code");
        String name = rs.getString("name");
        json.append("{'code':}");
        json.append(code);
        json.append(",'name':");
        json.append(name);
        json.append("\"},");
    }
}
String endJson = json.substring(0,json.length()-1)+"]";
response.setContentType("text/html;charset=UTF-8");
response.getWriter().println(json);

```

![image-20221108012214380](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221108012214380.png)

![image-20221108012358408](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221108012358408.png)

https://blog.csdn.net/qq_42114504/article/details/123439501

https://blog.csdn.net/qq_45950109/article/details/116723700





# 11.JQuery

```js
jQuery.extend(object)
为jQuery类添加类方法，可以理解为添加静态方法。
jQuery.extend({
    min: function(a, b) {
        return a < b ? a : b;
    },
    max: function(a, b) {
        return a > b ? a : b;
    }
});
jQuery.min(2, 3); //  2 
jQuery.max(4, 5); //  5



```

首先来看看JQuery中的 $() 这个符号，实际上这个符号在JQuery中相当于JQuery（）,即**$(this)=jquery()**;也就是说，这样可以返回一个jquery对象。

![image-20221110224452521](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221110224452521.png)
