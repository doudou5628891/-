## 索引原则

1. 索引不是越多越好 
2. 不要对经常变动的数据加索引
3. 小数据量的表不需要加索引
4. 索引一般加在常用来查询的字段上！

注意索引的 回表逻辑

count(**)     count(1)    count*(列名)

**当表的数据量大些时**，对表作分析之后，使用count(1)还要比使用count(*)用时多！ 当数据量在1W以内时，count(1)会比count(*)的用时少些，不过也差不了多少。

对于count(1)，InnoDB引擎会扫描主键索引树，因为不需要判断空值，直接按行累计+1，最后返回累计值。



1、如果在开发中确实需要用到count()聚合，那么优先考虑count(*)，因为mysql本身已经对于count(*)做了优化处理。

2、在有主键（或[复合主键](https://www.zhihu.com/search?q=复合主键&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"110729439"})）的情况下，主键作为计算条件，count(主键)快一些。

3、 如果表只有一个字段，则count(*)执行效率是最快的。

4、count(字段)，字段是非主键字段，这样的使用方式最好不要出现，因为它不会走索引。

5、使用count()聚合函数后，若有where条件，且where条件的字段未建立索引，则查询不会走索引，直接扫描了全表，不好。比如：SQL语句不要跟where score= 1；这样的条件，会导致不走索引，降低查询效率。除非该score字段已经建立了索引。

## 全文索引 fulltext

https://www.zhihu.com/search?type=content&q=mysql%20%E5%85%A8%E6%96%87%E7%B4%A2%E5%BC%95

版本支持

1. MySQL5.6之前，只有MyISAM支持全文索引

2. MySQL5.6之后，MyISAM和InnoDB均支持全文索引
3. 只有字段的数据类型char、varchar、text机器谢列才能建立全文索引

```sql
创建表时创建全文索引
create table fulltext_test (
    id int(11) NOT NULL AUTO_INCREMENT,
    content text NOT NULL,
    tag varchar(255),
    PRIMARY KEY (id),
    FULLTEXT KEY content_tag_fulltext(content,tag)  // 创建联合全文索引列
) ENGINE=MyISAM DEFAULT CHARSET=utf8;
在已存在的表上创建全文索引
create fulltext index content_tag_fulltext
    on fulltext_test(content,tag);
通过SQL语句创建全文索引
alter table fulltext_test
    add fulltext index content_tag_fulltext(content,tag);

删除
1.drop index content_tag_fulltext
	on fulltext_test;
2.alter table fulltext_test
	drop index content_tag_fulltext

如何使用（match+against）
select * from fulltext_test
	where match(content,tag) against('xxx xxx');

```

Conception

需求：希望通过关键字匹配的方法进行查询过滤，那么就需要基于相似度的查询，而不是原来精确数值比较。

解决：like+% 模糊匹配

问题：文本少的时候上述方法合适。但是对于大量文本数据检索，是不可想象的。

再解决：全文索引在大量的数据面前，能比like+%快N倍，但是全文搜索可能存在精度问题。



### 有关mysql 全文索引为什么无法match到4个字符一下的词语?

这是因为InnoDB下innodb_ft_min_token_size= 3 也就是说MYSQL的全文搜索只会对长度大于等于3的词语建立索引，而文章中的例子就是  ，所以无法匹配到4个字符以下的词语

## 两种全文索引

#### 自然语言的全文索引

默认情况下，或者使用 in natural language mode 修饰符时，match() 函数对文本集合执行自然语言搜索，上面的例子都是自然语言的全文索引。

自然语言搜索引擎将计算每一个文档对象和查询的相关度。这里，相关度是基于匹配的关键词的个数，以及关键词在文档中出现的次数。在整个索引中出现次数越少的词语，匹配时的相关度就越高。相反，非常常见的单词将不会被搜索，如果一个词语的在超过 50% 的记录中都出现了，那么自然语言的搜索将不会搜索这类词语。上面提到的，测试表中必须有 4 条以上的记录，就是这个原因。

这个机制也比较好理解，比如说，一个数据表存储的是一篇篇的文章，文章中的常见词、语气词等等，出现的肯定比较多，搜索这些词语就没什么意义了，需要搜索的是那些文章中有特殊意义的词，这样才能把文章区分开。

##### 布尔全文索引

在布尔搜索中，我们可以在查询中自定义某个被搜索的词语的相关性，当编写一个布尔搜索查询时，可以通过一些前缀修饰符来定制搜索。

MySQL 内置的修饰符，上面查询最小搜索长度时，搜索结果 ft_boolean*_*syntax 变量的值就是内置的修饰符，下面简单解释几个，更多修饰符的作用可以查手册

- **+** 必须包含该词
- **-** 必须不包含该词
- **>** 提高该词的相关性，查询的结果靠前
- **<** 降低该词的相关性，查询的结果靠后
- **(\*)星号** 通配符，只能接在词后面

对于上面提到的问题，可以使用布尔全文索引查询来解决，使用下面的命令，*a、aa、aaa、aaaa* 就都被查询出来了。

```sql
select * test where match(content) against('a*' in boolean mode);
```





1. 使用全文索引前，搞清楚版本支持情况；
2. 全文索引比 like + % 快 N 倍，但是可能存在精度问题；
3. 如果需要全文索引的是大量数据，建议先添加数据，再创建索引；
4. 对于中文，可以使用 MySQL 5.7.6 之后的版本，或者第三方插件。

```mnysql
DELIMITER $$   -- 写函数之前必须要的，标志
CREATE FUNCTION mock_data()
RETURN INT
BEGIN
	DECLARE num INT DEFAULT 1000000;
	DECLARE i INT DEFAULT 0;
	WHILE i<num DO
		--插入语句
		SET i=i+1;
	END WHILE
END;
INSERT INTO app_user(name,....字段) values(CONCAT('用户',i),'@qq.com',CONCAT('18',FLOOR(RAND()*(999999999-1000000000)+100000000)))
```

explain 很关键，他在语句前面可以返回执行计划中的每一步信息，而不是真正完全执行该语句。

所以，针对大表的测试，千万不要直接执行select语句，可能会使表锁死









## 用户操作

创建用户

create user renwei identified by 'password'

修改当前用户密码

set password = PASSWORD('*******')

修改指定用户密码

set password for renwei = PASSWORD('******')

重命名用户名

rename user renwei to renwei123

用户授权

```
grant all privileges on *.* to renwei123
```

查询授权

show grants for renwei123

show grants for root@localhost

root 用户权限：

```
grant all privileges on *.* to 'root@localhost' with grant option
```



撤销权限

```
revoke all privileges on *.* from renwei123
```

删除用户

drop  user renwei123





## 数据库备份

1.直接拷贝

2.可视化工具右键备份



3.命令行

```
mysqldump -hlocalhost -uroot -p123456 school(库) student(表) result(表A) ... >D:/a.sql
```

```
mysql -uroot -p123456
use school;
source d:/a.sql;
```



#### 软件开发中，关于数据库设计

分析需求：分析业务和需要处理的数据库需求

概要设计：设计关系图E-R图

#### 设计数据库步骤

手机信息，分析需求

​	表1 (字段，字段。。。)

​	表2 (字段，字段。。。)

​	表3 (字段，字段。。。)

标识实体之间的关系

做出e-r图

反应出表和表之间的关系 



## 数据库规范（三大范式）

关联查询的表不得超过3张，这是因为商业化的需求和目标

   1.故意给某表增加冗余，使多表查询变为但表查询

	2. 以前，订单表中 关联着 用户号 订单号 商品号，现在把商品的字段放在订单字段中，这时候就不需要关联商品表了



## JDBC

mysql 使用statement存在注入漏洞： 如语句 

```
select * from users where `Name` = '' or '1=1' and `password` ='' or '1=1'
```

但是用了PrepareStatement就不会在出现上述SQL注入的情况

这是因为本质上，PS 防止了sql注入，吧传递来的参数当作字符，所有的都用''包含，转义字符忽略





## **数据库优化**



![image-20221011112410686](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221011112410686.png)



![image-20221011120709110](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221011120709110.png)

1. 连接层

   ​	最上层是一些客户端和连接服务，包含本地socket通信和大多数基于客户端/服务端工具实现的类似于tcp/ip 通信。主要完成一些类似于连接处理、授权认证、及相关的安全方案。在该层上引入了线程池的概念。同样在该层上可以实现基于SSL的安全连接。服务器也会为安全接入的每个客户端验证它所具有的操作权限。

2. 服务层

   SQL接口，完成缓存的查询，SQL的分析和优化及部分内置函数的执行。所有跨存储引擎的功能也能在这一层实现，如过程、函数等。在该层，服务器会解析查询并创建相应的内部解析树，并对其完成相应的优化如确定查询表的顺序，是否利用索引等，最后生成相应的执行插座。

   如果是select语句，服务器还会查询内部的缓存。如果缓存空间足够大，这样在解决大量读操作的环境中能够很好的提升系统的性能。

3. 引擎层

   存储引擎层，存储引擎真正的负责了MySQL中数据的存储和提取，服务通过API与存储引擎进行通信。不同的存储引擎具有不同的功能，MyISAM和InnoDB

4. 存储层

   数据存储层，将数据存在运行于裸设备的文件系统之上，并完成与存储引擎的交互。

   

![image-20221011173518968](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221011173518968.png)

![image-20221011234546628](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221011234546628.png)

```mysql
select *

from tableA a

full outer join tableB b

on a.key = b.key

select *

from tableA a

full outer join tableB b

on a.key = b.key
where a.key is null
or b.key is null
为什么要用 is null 呢因为，链接之后，互相连接不上的如a.key 对应不了b.key 的 ，mysql会自动补null
但Mysql不支持  full outer join 语法

所以 full outer join只能如下表示   (什么样的操作 能够达到  合并与去重的操作呢？ union)
select * 
from tbl_emp a
left join tbl_dept b
on a.deptId = b.id
union  # 两个语句去重合并
select * 
from tbl_emp a
right join tbl_dept b
on a.deptId = b.id

而如果要把共有的部分去掉
select * 
from tbl_emp a
left join tbl_dept b
on a.deptId = b.id
where b.id is null
union  # 两个语句去重合并
select * 
from tbl_emp a
right join tbl_dept b
on a.deptId = b.id
where a.deptId is null;
```

### 那些情况需要创建索引

1. 主键自动建立唯一索引
2. **频繁作为查询条件**的字段应该创建索引，但**频繁更新的字段**不适合创建索引
3. 查询中**与其他表关联的字段**，外键关系建立索引
4. **where条件里用不到的字段** 不创建索引
5. 单键/组合索引的选择问题，who？（在高并发下倾向创建组合索引）
6. 查询中排序的字段，排序字段若通过索引去访问将大大提高排序速度
7. 查询中  **统计** 或者  **分组**的字段需要添加索引





## Explain + SQL

```mysql
explain select * from tbl_emp;
输出字段：
id  表示查询中执行select子句或操作表的顺序；id相同：执行顺序由上至下；id不同：若是子查询，id序号递增，id值越大优先级越高，越先被执行
select_type 
table
type
possible_keys
key
key_len
ref
rows
Extra

```

explain：

是什么：为了查看执行计划

能做什么：1.表的读取顺序；2.数据读取操作的操作类型；3.那些索引可以使用；4.哪些索引被实际使用；5.表之间的引用；6.每张表有多少行被优化器查询



#### 1.表的读取顺序(id)

id相同



id不同

![image-20221012113231033](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221012113231033.png)

相同不同，同时存在

![image-20221012114437161](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221012114437161.png)





#### 2.数据读取操作的操作类型（select_type）

![image-20221012124040411](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221012124040411.png)

#### 3.type

![image-20221012135807040](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221012135807040.png)

案例：

1.

![image-20221012140604213](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221012140604213.png)

执行顺序，由于id相等，所以由上至下，第一行对t2全表扫描all，第二行，有且只有一条数据匹配所以执行的eq_ref。



2.

![image-20221012141645085](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221012141645085.png)



3. ![image-20221012142223566](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221012142223566.png)
4. ![image-20221012142655646](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221012142655646.png)
5. ![image-20221012142729173](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221012142729173.png)

打比方：

eq_ref: 唯一性索引扫描。相当于研发部的总经理 有且只有一个

ref: 非唯一性索引扫描。研发部的数据库程序员，有很多

range: 只检索给定范围的行，使用一个索引来选择行。key列显示使用了哪个索引一般就是 where 语句中出现between、<、>、in等查询，这种范围扫描索引也比全表扫描要强。

index ：全索引扫描。与ALL的区别为。index类型只遍历索引树，这通常比ALL快，正如我们看到的B+树 一共只有三层，那么整个数据就会分为多份，我们只需要在某一份中招数据就行

能到达ref 和 range是最好的

#### 4 possible_keys

显示可能应用在这张表中的索引，一个或多个

查询涉及到的字段上若存在索引，则该索引将被列出，但不一定被查询实际使用。

#### 5 key

实际使用的索引。如果为NULL，则没有使用索引

查询中若使用了覆盖索引，则该索引仅出现在key列表中

![image-20221012143744737](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221012143744737.png)

上图例子

可能用到的索引-没有。实际用到的索引-没有=》索引没建立 ，索引失效。



![image-20221012144301439](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221012144301439.png)

index查询，理论用不到索引，实际上用到了复合索引 idx_col1_col2

像上述这种，要查的字段和建立的复合索引字段刚刚好吻合（包含顺序、名字），这种叫**覆盖索引**

#### 6 key_len

显示的值为索引字段最大可能长度，并非实际使用长度。

![image-20221012145348032](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221012145348032.png)

key_len计算方式

![img](https://pic3.zhimg.com/80/v2-4f2c557d264e2e6ca2c92a49a57ffeea_720w.webp)

#### 7 ref   reference

**显示索引的哪一列被使用了**，可能的话是一个常数(const)。哪些列或常量被用于查找索引列上的值。



 

#### 8 rows 越少越好

![image-20221012174137592](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221012174137592.png)



#### 9 extra



![image-20221012180756161](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221012180756161.png)



1. using filesor 说明mysql会对数据使用一个外部的索引排序，而不是按照表内的索引顺序进行读取。Mysql中无法利用索引完成的排序操作为”文件排序“。



![image-20221012193925097](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221012193925097.png)

上面两个例子的区别就是order by 后 是否有 col2。

按照  rule：查询中排序的字段，排序字段若通过索引去访问将大大提高排序速度



2. using temporary 使用了临时表保存中间结果，MySQL在对查询结果排序时使用临时表。常见于**排序order by 和分组查询group by**。



3. using index

   ![image-20221012205410042](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221012205410042.png)

所谓覆盖索引

假设你定义一个联合索引

```sql
CREATE INDEX idx_name_age ON user(name,age);
```

查询名称为 liudehua 的年龄：

```sql
mysql> select name, age from user where name = 'liudehua';
```

上述语句中，查找的字段 name 和 age 都包含在联合索引 idx_name_age 的索引树中，这样的查询就是覆盖索引查询。



![image-20221012210040938](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221012210040938.png)





实际例子

```mysql
# 查询category_id 为1 且comments大于1的情况下，views最多的article_id。
explain select id,author_id from article where category_id=1 and comments>1 order by views desc limit 1;
# 结果显示，type : ALL   Extra出现了Using filesort 

# 开始优化  创建复合索引 idx_article_ccv
create index idx_article_ccv on article('category_id','comments','views');
# alter table 'article' add index idx_article_ccv('category_id','comments','views');
# drop index id_article_ccv on article;


```

![image-20221012230623918](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221012230623918.png)

再来尝试运行

![image-20221012230706172](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221012230706172.png)

用到了索引，type 由ALL转为range,解决了全表扫描的问题，但还是使用了文件分类

结论：

这是因为BTree索引的工作原理？？？？

所以工作原理是什么

---------------------------------------------------------------------------------------------------------------------------------------------------------

#### BTree检索原理是什么？

![image-20221013113310619](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221013113310619.png)

浅蓝色是磁盘块，深蓝色数据项，黄色指针

**最下面一层是叶子节点，也是真实数据存储的地方**

每找一次就是一次磁盘io：查找29， 第一次加载磁盘块1到内存（一次IO），第二次加载磁盘块3，第三次加载磁盘块8

真实情况:3层b+数可以表示上百万数据，查找只需要三次IO，性能提升巨大，若没有索引，要百万次IO。



现在问题来了，假设name、gender列都有索引，那么SELECT * FROM user WHERE gender = 0; SELECT * FROM user WHERE name = 'swj';都能命中索引吗？

返回表中30%内的数据会走索引，返回超过30%数据就使用全表扫描。当然这个结论太绝对了，也并不是绝对的30%，只是一个大概的范围。

**回表**

当对一个列创建索引之后，索引会包含该列的键值及键值对应行所在的rowid。通过索引中记录的rowid访问表中的数据就叫回表。回表次数太多会严重影响SQL性能，**如果回表次数太多，就不应该走索引扫描，应该直接走全表扫描。**

EXPLAIN命令结果中的**Using Index意味着不会回表**，**通过索引就可以获得主要的数据**。**Using Where则意味着需要回表取数据**。





**我们可以通过SHOW STATUS LIKE 'Handler_read%';查看索引的使用情况：**

**Handler_read_key：**如果索引正在工作，Handler_read_key的值将很高。

**Handler_read_rnd_next：**数据文件中读取下一行的请求数，如果正在进行大量的表扫描，值将较高，则说明索引利用不理想。

**索引优化规则：**

1.如果MySQL估计使用索引比全表扫描还慢，则不会使用索引。

2.**返回数据的比例是重要的指标**，比例越低越容易命中索引。记住这个范围值——**30%**，后面所讲的内容都是建立在返回数据的比例在30%以内的基础上。

**前导模糊查询**不能命中索引。

name列创建普通索引：

3.前导模糊查询**不能命中索引**：EXPLAIN SELECT * FROM user WHERE name LIKE '%s%';

**非前导模糊查询**则**可以使用索引**，可优化为使用非前导模糊查询：EXPLAIN SELECT * FROM user WHERE name LIKE 's%';

4.数据类型出现隐式转换的时候不会命中索引，特别是当列类型是字符串，一定要将字符常量值用引号引起来。

EXPLAIN SELECT * FROM user WHERE name=1;

EXPLAIN SELECT * FROM user WHERE name='1';

5.复合索引的情况下，查询条件**不包含索引列最左边部分（不满足最左原则）**，不会命中符合索引。

​	name,age,status列创建复合索引：

```mysql
ALTER TABLE user ADD INDEX index_name (name,age,status);
```

user表索引详情：

```mysql
SHOW INDEX FROM user;
```

根据最左原则，可以命中复合索引index_name：

```mysql
EXPLAIN SELECT * FROM user WHERE name='swj' AND status=1;
```

**注意，最左原则并不是说是查询条件的顺序, 但这里需要注意的是order by 后的条件顺序 mysql是不进行优化的：**

```mysql
EXPLAIN SELECT * FROM user WHERE status=1 AND name='swj';
c1 = 'a1' order by c3,c2 # 不进行顺序优化，c2如果不再前面，c3索引是用不到的。
# 但若是
c1 = 'a1' and c2='a2' order by c3,c2 # 由于c2已经有了确定值，
# 另一种情况
from user order by pwd,name; # 全表扫描！
# order by 是需要给一个范围
# group by 基本上，分组之前都需要进行排序，会有临时表产生
c1 = 'a1' and c4='a4' group by c2,c3; # ref 正常 using where
c1 = 'a1' and c4='a4' group by c3,c2; # ref using where using temporary using filesort

```

**而是查询条件中是否包含索引最左列字段：**

```mysql
EXPLAIN SELECT * FROM user WHERE status=2 ;
```

union、in、or都能够命中索引，**建议使用in**。

**union:**

```mysql
EXPLAIN SELECT*FROM user WHERE status=1
```

UNION ALL

SELECT*FROM user WHERE status = 2;

**in:**

```mysql
EXPLAIN SELECT * FROM user WHERE status IN (1,2);
```

**or:**

```mysql
EXPLAIN SELECT*FROM user WHERE status=1 OR status=2;
```

**查询的CPU消耗：or>in>union**

**××××××**用**or分割开的条件**，如果**or前的条件中列有索引**，**而后面的列中没有索引**，那么涉及到的**索引都不会被用到**。

```mysql
EXPLAIN SELECT * FROM payment WHERE customer_id = 203 OR amount = 3.96;
# 在我的实验中，如果直接星号，也就说需要返回字段列中没有索引，依旧会进行全表扫描。
# 若尝试覆盖索引则不会，我认为覆盖索引最大的好处就是不会进行回表，如果进行回表，则mysql估计查询+回表不如直接全表扫描。
# 但如果有一列不是索引列，那么还是要扫描全表的。
```

因为or后面的条件列中没有索引，那么**后面的查询肯定要走全表扫描**，在**存在全表扫描的情况下**，就**没有必要多一次索引扫描增加IO访问**。

×××××××××××××××**负向条件查询不能使用索引，可以优化为in查询。**×××××××××××××××××××

负向条件有：**!=、、not in、not exists、not like**等。

```mysql
# exists 与 in 最大的区别在于 in引导的子句只能返回一个字段，比如：
select name from student where sex = 'm' and mark in (select 1,2,3 from grade where ...)
# in子句返回了三个字段，这是不正确的，exists子句是允许的，但in只允许有一个字段返回，在1，2，3中随便去了两个字段即可。
```

exists就是说 ()中的内容是true还是false，若为true则，相当于 where true

```mysql
select * from user where TRUE;
```

**select * from 表；查询出表中所有数据，性能比较差；**

**select 常量 from 表；查询出结果是所有记录数的常量，性能比较高；**

select count (*)和select count(1)





status列创建索引：

ALTER TABLE user ADD INDEX index_status (status);

user表索引详情：

SHOW INDEX FROM user;

负向条件不能命中缓存：

EXPLAIN SELECT * FROM user WHERE status !=1 AND status != 2;

可以优化为in查询，但是前提是区分度要高，返回数据的比例在30%以内：

EXPLAIN SELECT * FROM user WHERE status IN (0,3,4);

范围条件查询可以命中索引。范围条件有：、>=、between等。

status,age列分别创建索引：

ALTER TABLE user ADD INDEX index_status (status);

ALTER TABLE user ADD INDEX index_age (age);

user表索引详情：

SHOW INDEX FROM user;

范围条件查询可以命中索引：

EXPLAIN SELECT * FROM user WHERE status>5;

范围列可以用到索引（联合索引必须是最左前缀），但是范围列后面的列无法用到索引，索引最多用于一个范围列，如果查询条件中有两个范围列则无法全用到索引：

EXPLAIN SELECT * FROM user WHERE status>5 AND age

如果是范围查询和等值查询同时存在，优先匹配等值查询列的索引：

EXPLAIN SELECT * FROM user WHERE status>5 AND age=24;

数据库执行计算不会命中索引。

EXPLAIN SELECT * FROM user WHERE age>24;

EXPLAIN SELECT * FROM user WHERE age+1>24;

计算逻辑应该尽量放到业务层处理，节省数据库的CPU的同时最大限度的命中索引。

利用覆盖索引进行查询，避免回表。

被查询的列，数据能从索引中取得，而不用通过行定位符row-locator再到row上获取，即“被查询列要被所建的索引覆盖”，这能够加速查询速度。

user表的索引详情：

因为status字段是索引列，所以直接从索引中就可以获取值，不必回表查询：

Using Index代表从索引中查询：

EXPLAIN SELECT status FROM user where status=1;

当查询其他列时，就需要回表查询，这也是为什么要避免SELECT*的原因之一：

EXPLAIN SELECT * FROM user where status=1;

建立索引的列，不允许为null。

单列索引不存null值，复合索引不存全为null的值，如果列允许为null，可能会得到“不符合预期”的结果集，所以，请使用not null约束以及默认值。

remark列建立索引：

ALTER TABLE user ADD INDEX index_remark (remark);

IS NULL可以命中索引：

EXPLAIN SELECT * FROM user WHERE remark IS NULL;

IS NOT NULL不能命中索引：

EXPLAIN SELECT * FROM user WHERE remark IS NOT NULL;

虽然IS NULL可以命中索引，但是NULL本身就不是一种好的数据库设计，应该使用NOT NULL约束以及默认值。

更新十分频繁的字段上不宜建立索引：因为更新操作会变更B+树，重建索引。这个过程是十分消耗数据库性能的。

区分度不大的字段上不宜建立索引：类似于性别这种区分度不大的字段，建立索引的意义不大。因为不能有效过滤数据，性能和全表扫描相当。另外返回数据的比例在30%以外的情况下，优化器不会选择使用索引。

业务上具有唯一特性的字段，即使是多个字段的组合，也必须建成唯一索引。虽然唯一索引会影响insert速度，但是对于查询的速度提升是非常明显的。另外，即使在应用层做了非常完善的校验控制，只要没有唯一索引，在并发的情况下，依然有脏数据产生。

多表关联时，要保证关联字段上一定有索引。

创建索引时避免以下错误观念：索引越多越好，认为一个查询就需要建一个索引；宁缺勿滥，认为索引会消耗空间、严重拖慢更新和新增速度；抵制唯一索引，认为业务的唯一性一律需要在应用层通过“先查后插”方式解决；过早优化，在不了解系统的情况下就开始优化。





----------------------------------------------------------------------------------------------------------------------------------------------------------------



先排序category_id，如果遇到相同的category_id 则再排序comments，如果遇到相同的comments 则再排序views



我认为这里的排序就是先在索引查找的意思，如下图找完12

 select * from T1 where a = 12 and b = 14 and c = 3

![在这里插入图片描述](https://img-blog.csdnimg.cn/7835e2d97393467a8e3dee32df72744a.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0Nzg3ODE2,size_16,color_FFFFFF,t_70)

关键： <>  这样的范围查找，索引会导致失效，继续文件内排序(filesort)

```mysql
drop index id_article_ccv on article;

# 开始优化  创建复合索引 idx_article_cv 绕开comments
create index idx_article_ccv on article('category_id','comments','views');
```

![image-20221013121735853](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221013121735853.png)

type 变为了ref, Extra中的Using filesort 也消失了

### 如何进行索引的建立和优化？

##### 1.单表情况下

就是上面的那个例子

双表

三表

left join + left join

**对被连的那个的字段设置**，也就是保证Join语句中被驱动表Join条件字段已经被索引。

**当无法保证被驱动表的join条件字段被索引 且 内存资源充足情况下，不要太吝啬JoinBuffer的设置**



### 如何避免索引失效？

1. 全值匹配我最爱

```mysql
show index from staffs;
# 左前缀法则，指的是查询从索引的最左前列开始并且不跳过索引中的列，不然只能用一部分
带头大哥不能死，中间不能断
```

2. 左前缀法则，指的是查询从索引的最左前列开始并且不跳过索引中的列，不然只能用一部分。带头大哥不能死，中间不能断
3. 隐式转换导致全表扫描，不要再索引列上做任何操作（计算、函数、（自动or手动类型转换））
4. ![image-20221013161438526](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221013161438526.png)

存储引擎不能使用索引中范围条件 右边的列

5. 尽量使用覆盖索引（只访问索引的查询（索引列和查询列一致））减少select* 要具体到列

6. mysql在使用不等于（！=或者<>）时，无法使用索引

7. is null和 is not null无法使用索引,, 在我的版本中 is null 是使用索引的using index condition  ,type  = ref

   而is not null  , type = all

![](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221013232854212.png)

8. like 以通配符开头('%abc...') mysql 索引失效会变成全表扫描的操作

![image-20221014000006023](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221014000006023.png)

type =  ALL，全表扫描

![image-20221014001228055](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221014001228055.png)

type = range, rows 1 len = 93，,并且使用了索引，所以%只能加在右侧，索引才会被使用，避免了索引失效。(mysql 8 )

(mysql 10 type = index)

**问题来了：实际需求必须要左右都要%，这该怎么办。**

要进行覆盖索引，两边加%，索引就不会失效。

![image-20221014012132486](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221014012132486.png)

SQL> select * from emp where instr(job,'RE')>0 and instr(ename,'A')>0 and instr(mgr,'3')>0;

EMPNO ENAME    JOB     MGR HIREDATE      SAL    COMM DEPTNO

```mysql
create index idx_c1_c2_c3 on user(c1,c2,c3);
explain select c1,c2,c3 from user c1='a1' and c2 like 'kk%' and c3='a3';
# 可能你会认为c2 进行了范围查询，c3无法进行索引查询。但其实c123都能索引
explain select c1,c2,c3 from user c1='a1' and c2 like '%kk' and c3='a3';
# 这种情况下c2就用不了索引了，只有c1使用，就算是覆盖索引也没有办法，因为索引在c2那里已经断了
explain select c1,c2,c3 from user c1='a1' and c2 like 'k%kk' and c3='a3';
# 这中三个索引都可以用到。
```



----- ---------- --------- ----- ----------- --------- --------- ------

Executed in 0.063 seconds

看到了吧，，时间上的差异很明显，INSTR在一瞬间执行完成，因为这个是查找的字段，而非走全表扫描

9. #### 字符串不加单引号索引失效

10. #### 少用or，用它来连接时索引失效

    因为or后面的条件列中没有索引，那么**后面的查询肯定要走全表扫描**，在**存在全表扫描的情况下**，就**没有必要多一次索引扫描增加IO访问**。













### tips：**Oracle B-tree、位图、全文索引**三大索引性能比较及优缺点罗列一下

**1、B-Tree索引**

场合：**非常适合数据重复度低的字段** 例如 身份证号码 手机号码 QQ号等字段，常用于主键 唯一约束，一般在在线交易的项目中用到的多些。

原理：**一个键值对应一行（rowid）** 格式： 【索引头|键值|rowid】

优点：当没有索引的时候，oracle只能全表扫描where qq=40354446 这个条件那么这样是灰常灰常耗时的，当数据量很大的时候简直会让人崩溃，那么有个B-tree索引我们就像翻书目录一样，直接定位rowid立刻就找到了我们想要的数据，实质减少了I/O操作就提高速度，它有一个显著特点查询性能与表中数据量无关，例如 查2万行的数据用了3 consistent get,当查询1200万行的数据时才用了4 consistent gets。

当我们的字段中使用了主键or唯一约束时，不用想直接可以用B-tree索引

缺点：**不适合键值重复率较高的字段上使用**

2、位图索引  Bitmap index
场合：列的基数很少，可枚举，重复值很多，数据不会被经常更新
原理：一个键值对应很多行（rowid）， 格式：键值 start_rowid  end_rowid 位图
优点：OLAP 例如报表类数据库 重复率高的数据 特定类型的查询例如count、or、and等逻辑操作因为只需要进行位运算即可得到我们需要的结果
缺点：不适合重复率低的字段，还有经常DML操作（insert，update，delete），因为位图索引的锁代价极高，修改一个位图索引段影响整个位图段，例如修改
一个键值，会影响同键值的多行，所以对于OLTP 系统位图索引基本上是不适用的

3、**全文索引 Text index**
定义：**全文索引就是通过将文字按照某种语言进行词汇拆分，重新将数据组合存储，来达到快速检索的目的**
场合：**当字段里存储的都是文本时适合用全文索引，常用于搜索文字**
优点：全文索引不是按照键值存储的，而是按照分词重组数据，常用于模糊查询Where name like '%leonarding%'效率比全表扫描高很多，适用OLAP系统，
OLTP系统里面用到的并不多。
缺点：**全文索引会占用大量空间有时比原表本身占的空间还多，bug较多，维护困难。**





## 查询截取分析

1. #### 查询优化

   实际工作中：

   1. **观察，挑出生产慢的SQL情况。**
   2. **开启慢查询日志，设置阈值，比如超过5s的就是慢SQL，并将它捕获。**
   3. **explain + 慢SQL分析**
   4. **show profile 查询SQL在Mysql服务器里面的执行细节和生命周期情况。**
   5. 运维经理orDBA，**进行SQL数据库服务器的参数调优**。

​	查询优化方法

```mysql
# 永远小表驱动大表, 即小的数据集驱动大的数据集
select * from A where id in (select id from B)
等价于：
for select id  from B
	for select * from A where A.id = B.id 
# 也就是说B数据集小于A数据集，这种情况，用in优于exists。
换一种exists写法
select * from A where exists (select 1 from B where B.id=A.id)
等价于：
for select * from A
	for select * from B where B.id = A.id
# 也就说 拿A去跟B一个一个比较id有则返回true，所以这样的话就是B

这是sql的机读顺序问题，in的时候先执行括号里的查询，所以括号里的表要小，exists的时候先执行外查询，所以括号外的表要小

tips exist()中的内容 可以说 select* 也可以是select 1 ，最后返回的都是 true or false, 这样可以决定外层主查询的的数据结果是否保留。
最好是使用in去优化sql语句
总结 : in 后面跟的是小表   exists 后面跟的是大表
```

```mysql
order by 应该尽量使用index方式排序，避免使用FileSort方式排序
要尽可能在索引列上完成排序操作，遵照索引建的最佳左前缀


针对参数调优的问题：

如果不在索引列上必然会产生额外排序，额外排序方式

双路排序

单路排序   一次性读取到buffer中进行排序，但若是buffer内存不够大，则会按照设置的sort_buffer="xxx"的大小读取数据并排序，最后多路合并，多次的IO可能会导致效率更低

所以可以尝试增大sort_buffer_size的参数社会i

优化策略：
1. 增大sort_buffer_size参数设置
2. 增大max_length_for_sort_data参数设置
目的都是为了提高order by的速度


3.1单路排序步骤
1.从索引name字段中，查询到满足条件的数据name='jarye'条件主键的id
2.根据主键id取出整行的数据，缓存到sort buffer中
3.重复执行1,2步骤流程
4.对sort buffer中的数据实现排序给客户端

3.2双路排序步骤
1.从索引name字段中，查询到满足条件的数据name=‘jarye’条件主键的id
2.根据主键id，获取排序的字段和主键id缓存到sort buffer中
3.重复执行1,2步骤流程
4.对sort buffer中的数据实现排序
5.根据排序好的主键id和position，在从原来表中根据id查询数据给客户端。

3.3单路和双路排序方式的选择及优缺点
Mysql可以通过 max_length_for_sort_data 默认是1024字节大小
A.如果查询字段总的长度大小比设定的max_length_for_sort_data 要小，则使用单路排序方式；
B.如果查询字段总的长度大小比设定的max_length_for_sort_data 要大，则使用多路排序方式

单路排序算法：sort buffer 缓冲区缓存整个行的数据，在根据排序字段进行
排序，返回数据给客户端，缺点：占用内存 优点：避免回表查询
双路排序算法：sort buffer 缓冲区只缓存主键id和排序字段在内存中，在根据排序字段进行排序，在做一次回表查询，根据主键id查询该行数据返回给客户端。

```

![image-20221015010519006](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221015010519006.png)

![image-20221015001256494](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221015001256494.png)

上图可以发现在age为范围查询时，就是不是const的时候，后面的order by 若没有带头大哥时，将会产生文件排序。同时注意order by后不优化顺序



![image-20221015004126981](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221015004126981.png)

tips: order by 默认升序ASC,

```mysql
explain select name,pwd,nickname from user where name like'x%' order by name DESC;  # 不需要额外排序
explain select name,pwd,nickname from user where name like'x%' order by name DESC,pwd ASC; # 需要额外排序
explain select name,pwd,nickname from user where name like'x%' order by name ASC,pwd ASC; # 不需要
explain select name,pwd,nickname from user where name like'x%' order by name ASC,pwd DESC; # 需要
这说明，索引以及有了默认的顺序，如果两者能保持一致 ASC or DESC 则不要重新排序，正向或反向输出即可。若有其中任意一个产生与其他几个索引列顺序不一致的情况，则需要额外排序
```

stringbuffer 线程安全，stringbuilder线程不安全，后者更快

**一条SQL实际上可以分为三步。**

1.得到数据

2.处理数据

3.返回处理后的数据

比如上面的这条语句

```mysql
select sid from zhuyuehua.student where sid < 50000 and id < 50000 order by id desc
```

第一步：根据where条件和统计信息生成执行计划，得到数据。

第二步：将得到的数据排序。

当执行处理数据（order by）时，数据库会先查看第一步的执行计划，看order by 的字段是否在执行计划中利用了索引。如果是，则可以利用索引顺序而直接取得已经排好序的数据。如果不是，则排序操作。

第三步：返回排序后的数据。

**总结：**

当**order by 中的字段出现在where条件中**时，才会**利用索引而不排序**，更准确的说，**order by 中的字段在执行计划中利用了索引时，不用排序操作。**

我的理解：

使用了复合索引情况下，你在where中给出了第一个索引字段的要求，若是范围，则仅能确定某几组这个范围内的数据，但后面的几个索引字段顺序整体上看是 无序的，

所以order by后没有带头大哥的情况下，直接orderby后面的索引列是 需要filesort，再排序的。

而orderby后有带头大哥的情况下，带头大哥肯定不需要排，但带头大哥已经是个范围了，后面的还是要排序

就想象下面这个排好序的数据结构，你觉得对应的列是直接用还是需要再排序？显而易见吧

a b rowid
1 1 1
1 1 12
1 1 14
1 2 6
1 2 8

这个结论不仅对order by有效，对其他需要排序的操作也有效。比如group by 、union 、distinct等。



### 慢查询日志操作

```mysql
# 查看慢查询日志
show variables like '%slow_query_log%';
# 开启慢查询日志，当然这个是临时的
set global slow_query_log = 1;
# 那么什么样的SQL能被记录呢？时间阈值由参数long_query_time 控制，默认10s
show variables like 'long_query_time%';
set global long_query_time = 3; # 设置为3s
# 从文件系统中查看日志
cat atguigu-slow.log
# 查看是否有慢的sql
show global status like '%Slow_queries%';

# 永久生效慢查询日志方法，在mysqlid文件中配置：
slow_query_log = 1;
slow_query_log_file=/var/lib/mysql/atguigu-slow.log;
long_query_time = 3;
log_output=FILE;

# mysql提供的日志分析工具   mysqldumpslow
# 得到 返回记录集最多的 10个sql 另外建议 输入命令时结合| more使用，否则可能会爆屏幕
mysqldumpslow -s r -t 10 /var/lib/mysql/atguigu-slow.log  | more
# 得到 访问次数最多的 10个sql
mysqldumpslow -s c -t 10 /var/lib/mysql/atguigu-slow.log  | more

```

![image-20221015125434805](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221015125434805.png)

### 函数与存储过程 创建100w条数据

**存储过程和函数区别**

一、含义不同

1、存储过程：**存储过程是SQL 语句和可选控制流语句的预编译集合**，**以一个名称存储并作为一个单元处理**。

2、函数：**是由一个或多个 SQL 语句组成的子程序**，**可用于封装代码以便重新使用**。 **函数限制比较多，如不能用临时表，只能用表变量等**

二、使用条件不同

1、存储过程：**可以在单个存储过程中执行一系列 SQL 语句**。而且**可以从自己的存储过程内引用其它存储过程**，这可以简化一系列复杂语句。

2、函数：自定义函数诸多限制，有许多语句不能使用，许多功能不能实现。函数可以直接引用返回值，用表变量返回记录集。但是，用户定义函数不能用于执行一组修改全局数据库状态的操作。

三、执行方式不同

1、存储过程：存储过程**可以返回参数**，**如记录集**，函数**只能返回值或者表对象**。存储过程的**参数有in，out，inout三种**，存储过程**声明时不需要返回类型**。

2、函数：函数**参数只有in**，而**函数需要描述返回类型**，且函数**中必须包含一个有效的return语句**。

以上就是存储过程和函数有什么区别的详细内容，更多请关注php中文网其它相关文章！



1. 存储过程插入

   首先，**需要把表中的索引去掉**，插入完成后再创建索引；还要**关闭事务的自动提交**，每次插入都要自动提交事务是很耗时的。

​		

```mysql
use test_db;
# 删除已存在的procedure 
drop procedure if exists BatchAInsert;
delimiter $$
create procedure BatchInsert(IN inistId int,IN loop_counts INT)
begin
	declare var int;
	declare id int;
	set var = 0;
	set id = inistId;
	set autocommit = 0; -- 关闭自动提交事务，提高查询效率
	while var<loop_counts do
		insert into 'xxx_biao' ('product_no','xxx_channel_code','business_no')
		values (concat('20220704',10000000000+id), rand()*50,'RS20190719143225916727');
		set id = id+1;
		set var = var+1;
	end while;
	commit;
end $$;
delimiter;


call BatchInsert(1,1000000)  -- 调用存储过程
		
```

2.通过应用代码插入
也就是说通过在应用代码中（我们自己造数据的测试代码）使用MySQL的batch insert方式插入：

insert into table(id,col) values(1,'foo'),(2,'bar')…
**注意分批提交的大小，1000条插入一次还是3000条插入一次，也是会影响性能的**

3.通过MySQL自带的mysqlImport工具
即使用load data通过文件导入







一.慢查询日志

二.批量数据脚本

三.show profile

这个是mysql提供的，用来分析当前会话中语句执行的资源消耗情况。可以用于SQL的调优	

默认情况下处于关闭状态，并保存最近15次的运行结果

```mysql
https://zhuanlan.zhihu.com/p/105403992
# 第一步 查看是否支持
show variables like 'profiling'; # 查看profile状态
# 第二步 开启功能
set profiling = on;  # 开启
# 第三步 运行SQL 随便运行什么都可以

# 第四步 查看结果,show profiles;
若其中出现了以下的items ，说明该条SQL语句执行效率差。
converting heap to MyISAM 查询结果太大，内存不够用，需要使用磁盘
creating tmp table 创建临时表：1.拷贝数据到临时表 2.用完再删除 
copying to tmp table on disk 把内存中临时表复制到磁盘，很危险！！！
locked


通常情况下sending data 耗时长，这表示mysql线程开始访问数据行并将数据返回给客户端，它既包含有访问数据的操作(大量的磁盘IO)也包含有返客户端的操作，

# 第五步 诊断SQL show profile cpu, block io for query上一步前面的问题数字好


```

![image-20221016140650560](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221016140650560.png)





有关group by 的复习

```mysql
#MySQL错误-this is incompatible with sql_mode=only_full_group_by完美解决方案,使用any_value或者改sql_mode
https://blog.csdn.net/u012660464/article/details/113977173

# 有关group by 的原理和优化思路
https://blog.csdn.net/qq_40124555/article/details/122673993

# where 后面不能使用非聚合函数
https://blog.csdn.net/weixin_35196629/article/details/113464023

```

​	

四.全局查询日志 --不要在生产环境启用，要在测试环境中

```mysql
set global general_log = 1 ;
set global log_output='TABLE';
# 此后运行的sql语句 都会保存在这个表里面
select * from mysql.general_log;
```



# MySQL锁机制

锁是计算机**协调多个进程或线程**并发访问某一资源的机制。

在数据库中，除传统的计算资源（CPU、RAM、I/O）的竞争用以外，数据也是一种供许多用户共享的资源。

如何保证数据并发访问的一致性、有效性？锁的冲突是影响数据库并发访问性能的原因之一。



锁的分类：

对数据操作的类型分  **读锁和写锁** 。

读锁（共享锁）：针对同一份数据，多个读操作可以同时进行而不会相互影响。

写锁（排它锁）：当前写操作没有完成前，阻断其他写锁和读锁。

对数据操作的粒度分 **表锁和行锁**

三锁： 

表锁（偏读） 



行锁（偏写） 

页锁

存在一种情况，两个会话分别是session1 and session2

```mysql
# session1 
lock table mylock read;
select * from mylock; # 读操作可以
update mylock set name='a3' where id=1; #失败，因为有读锁
# session2 
select * from mylock; # 正常
update mylock set name='a3' where id=1; #失败，会阻塞

# 一旦session1 释放读锁
unlock tables;

总结： 读锁会阻塞写，但不会堵塞读。而写锁会把读和写都阻塞，只能自己写
show open TABLES;
show status like 'table%';
Table_locks_immediate 整个表被锁次数（myisam）
Table_locks_waited
由于myisam没有行级锁，每次锁都是锁表。 它读写锁调度是 写优先。写锁后，其他线程不能做任何操作，所以会经常是
```

 InnoDB和MyISAM最大的两个不同：一是支持事务(ACID)，二是采用了行级锁





## MVCC与间隙锁

### MVCC

https://blog.csdn.net/SIESTA030/article/details/123113437

MVCC即多版本[并发](https://so.csdn.net/so/search?q=并发&spm=1001.2101.3001.7020)控制，MVCC是一种并发控制的方法，一般在数据库管理系统中，实现对数据库的并发访问，在编程语言中实现事务内存。

**为了提高数据库的并发性能，用更好的方式去处理读-写冲突**，做到==**即使有读写冲突时**，**也能做到不加锁，非阻塞并发读**==。



**当前读与快照读**

当前读：读取的是记录的最新版本，并且当前读返回的记录都会上锁，保证其他事务不会在并发修改

快照读：读取的是记录的可见版本（有可能是历史版本），不加锁





### insert 用到的锁

```mysql
# insert 用到的锁
https://blog.csdn.net/youanyyou/article/details/121397691
insert 会在检查锁冲突和写数据之前，会对记录所在的页加一个 RW-X-LATCH 锁，执行完写数据后在释放

https://article.itxueyuan.com/pWlDb
show engine innodb mutex 进行查看latch

可能用到的情景和对性能的影响
使用情景：
1.select *** for update 的使用场景   ××××××可以锁一行
为了让自己查到的数据确保是最新数据，并且查到后的数据只允许自己来修改的时候，需要用到 for update 子句。
2.select *** lock in share mode 使用场景
为了确保自己查到的数据没有被其他的事务正在修改，也就是说确保查到的数据是最新的数据，并且不允许其他人来修改数据。但是自己不一定能够修改数据，因为有可能其他的事务也对这些数据 使用了 in share mode 的方式上了 S 锁。
性能影响：
1.select for update 语句，相当于一个 update 语句。在业务繁忙的情况下，如果事务没有及时的commit或者rollback 可能会造成其他事务长时间的等待，从而影响数据库的并发使用效率。
2select lock in share mode 语句是一个给查找的数据上一个共享锁（S 锁）的功能，它允许其他的事务也对该数据上 S锁，但是不能够允许对该数据进行修改。如果不及时的commit 或者rollback 也可能会造成大量的事务等待。
for update 和 lock in share mode 的区别：前一个上的是排他锁（X 锁），一旦一个事务获取了这个锁，其他的事务是没法在这些数据上执行 for update ；后一个是共享锁，多个事务可以同时的对相同数据执行 lock in share mode。
```

latch一般称为闩锁（**轻量级别的锁**），因为其要求**锁定的时间必须非常短**。若持续的时间长，则应用的性能会非常差。在Innodb存储引擎中，latch又可以分为**mutex（互斥量）和RW-Lock（读写锁）**。

![img](https://imgs.itxueyuan.com/1178354-20170609134641825-1568350173.png)



间隙锁的危害：









# 联合索引在B+树上的结构介绍(转载)--深度理解联合索引的好文

https://blog.csdn.net/weixin_30299709/article/details/95469432

![20180217145611191](C:\Users\MSi\Desktop\JavaSE\20180217145611191.png)

如图，联合索引(年龄, 姓氏,名字)，叶节点上data域存储的是三个关键字的数据。且是按照年龄、姓氏、名字的顺序排列的。

因此，如果执行的是：
select * from STUDENT where 姓氏='李' and 名字='安';
或者
select * from STUDENT where 名字='安';
那么当执行查询的时候，是无法使用这个联合索引的。因为联合索引中是先根据年龄进行排序的。如果年龄没有先确定，直接对姓氏和名字进行查询的话，就相当于乱序查询一样，因此索引无法生效。因此查询是全表查询。

如果执行的是：
select * from STUDENT where 年龄=1 and 姓氏='李';
那么当执行查询的时候，索引是能生效的，从图中很直观的看出，age=1的是第一个叶子节点的前6条记录，在age=1的前提下，姓氏=’李’的是前3条。因此最终查询出来的是这三条，从而能获取到对应记录的地址。
如果执行的是：
select * from STUDENT where 年龄=1 and 姓氏='黄' and 名字='安';
那么索引也是生效的。

而如果执行的是：
select * from STUDENT where 年龄=1 and 名字='安';
那么，索引年龄部分能生效，名字部分不能生效。也就是说索引部分生效。

因此我对联合索引结构的理解就是B+Tree是按照第一个关键字进行索引，然后在叶子节点上按照第一个关键字、第二个关键字、第三个关键字…进行排序。



```
假设数据 表T (a,b,c) rowid 为物理位置
rowid a b c
(1)  1 1 1
(2)  2 1 13
(3)  2 2 14
(4)  1 3 3
(5)  2 3 12
(6)  1 2 5
(7)  2 3 9
(8)  1 2 2
(9)  1 3 6
(10) 2 2 11
(11) 2 2 8
(12) 1 1 7
(13) 2 3 15
(14) 1 1 4
(15) 2 1 10
```

 

```
当你创建一个索引 ``create` `index` `xxx ``on` `t(a,b),
则索引文件逻辑上等同于如下
a b rowid
1 1 1
1 1 12
1 1 14
1 2 6
1 2 8
1 3 4
1 3 9
2 1 2
2 1 15
2 2 3
2 2 10
2 2 11
2 3 5
2 3 7
2 3 13
```

 

```
当``select` `* ``from` `T ``where` `a=1 ``and` `b=3 的时候，
数据库系统可以直接从索引文件中直接二分法找到A=1的记录，
然后再B=3的记录。
但如果你 ``where` `b=3 则需要遍历这个索引表的全部！
```
