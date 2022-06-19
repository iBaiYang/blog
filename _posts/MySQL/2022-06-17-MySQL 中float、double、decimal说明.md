---
layout: post
categories: MySQL
title: MySQL 中float、double、decimal说明
meta: MySQL 中float、double、decimal说明
---
* content
{:toc}

## 正文

我们知道在MySQL中有3种类型可以表示实数，分别是float,double和decimal。
如何合理得使用这三种类型：什么情况下选择float，什么情况下选择double，什么情况下选择decimal。

![]({{site.baseurl}}/images/20220617/20220617163107.png)

### 举个例子

假如我们有一张表，用来存储用户的积分，表定义如下：
```
CREATE TABLE `f` (
  `f1` float(10,2) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf-8
```

然后向这个表里插入131072.32的积分值，如下所示：
```
mysql> insert into f value (131072.32);
Query OK, 1 row affected (0.00 sec)
mysql> select * from f;
+-----------+
| f1        |
+-----------+
| 131072.31 |
+-----------+
1 row in set (0.00 sec)
```

然后会惊奇的发现，这个用户的积分少了0.01，虽然这0.01的积分并不足于引起我们的注意，但是问题还是来了
* 丢失数据是否是正常现象？
* 为什么会少0.01，有没有可能少0.02，或者少1，少10甚至少100？
* 怎么样才能让我们的数据准确？

### 官方怎么看数据丢失

首先遇到问题，第一想到的就是官方找答案，我们翻阅官方文档，关于float和double有这样一段描述

    For FLOAT, the SQL standard permits an optional specification of the precision (but not the range of the exponent) in bits following the keywordFLOAT in parentheses. MySQL also supports this optional precision specification, but the precision value is used only to determine storage size. A precision from 0 to 23 results in a 4-byte single-precision FLOAT column. A precision from 24 to 53 results in an 8-byte double-precision DOUBLEcolumn.

这段话大致可以这样描述：数据的精确度取决于分配给每种数据类型的存储长度，其中float分配了4字节，而double分配了8字节；
并且数据的这种不准确是正常现象。采用float和double本来就是不准的！

### 实数保存和分配存储长度的关系

在MySQL官方里有这样一句话，数据准确度取决于分配给数据类型存储的长度。
在查阅资料可知，单精度类型float和双精度类型double在计算机中存储的时候，由于计算机只能存储二进制，
所以浮点型数据在存储的时候，必须转化成二进制。在计算机中，float型数据的存储格式为 

![]({{site.baseurl}}/images/20220617/20220617163628.png)

比如8.25用二进制表示可表示为1000.01，转成指数的形式1.00001*2^3,在计算机中 

![]({{site.baseurl}}/images/20220617/20220617163700.png)

我们知道对于float类型的数据，只分配了32位的存储空间（1位符号位+8位指数位+23位小数位），
对于double类型值分配了64位（1位符号位+11位指数位+52位小数位），
但是并不是所有的实数都能转成32位或者64位的二进制形式，如果超过了，就会出现截断，这就是误差的来源。
比如将上面例子中的131072.32转成二进制后的数据为：

    100000000000000000.0101000111101011100001010001111010111000010100011111…

这是一个无穷数，对于float类型，只能截取前32位进行存储，对于double只能截取前64位进行存储。所以

131072.32保存为float类型是存储形式为：

    01001000000000000000000000010100;

131072.32保存为double类型的格式为：

    0100000100000000000000000000001010001111010111000010100011110101

针对float情况，至少我们可以得出结论：
1. 如果一个float型数据转成二进制后的第32位之后都是0，那么数据是准的
2. 如果一个float型数据转成二进制后的第32位之后不全为0，则数据就会存在误差

### 重新说明float(M, D)两个参数的意义

这两个参数表示一共能存M位，其中小数点后占D位。比如float(3,1)表示一共3位，其中小数点后1位数字。这里会有两个误区：
* 数据的精度总是能精确到D位，也就是数据的不精确一定出现在小数点后
* 数据存储的时候只能存储到D位小数

第一个误区，如果对于float，4字节的存储空间连整数的存储不下的时候，连整数都有误差的，更何况小数，
所以存储空间大小决定存储精度，和D值无关。来看这样一个例子：
```
mysql> create table f2 (f1 float(15,2));
Query OK, 0 rows affected (0.01 sec)
mysql> insert into f2 values (123456789.39);
Query OK, 1 row affected (0.00 sec)
mysql> select * from f2;
+--------------+
| f1           |
+--------------+
| 123456792.00 |
+--------------+
1 row in set (0.00 sec)
```

最后你会发现，连整数都不准了，小数被完全抹去了。

第二个误区，对于存储而言，是和D无关的一个参数。因为浮点型数据最终都要被转成二进制进行存储。
并且对于float，这个二进制只能有32位0和1的组合。看下面的例子：
```
mysql> select * from f;
+-----------+
| f1        |
+-----------+
| 131072.31 |
+-----------+
1 row in set (0.00 sec)
mysql> alter table f modify f1 float(10,4);
Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0
mysql> select * from f;
+-------------+
| f1          |
+-------------+
| 131072.3125 |
+-------------+
1 row in set (0.00 sec)
```

可以看到，修改一下显示宽度D，这个时候可以看到MySQL真正存储的数字是131072.3125

### 怎么样才能存储一个准确的数据

如果采用float或者double类型的话，数据有时候完全准确的，有时候是不准确的，怎么才能存储一个准确的数字，
完全看你需要存什么样的数据，假如存储一个8.25这样的数字，那永远都是准确的。
但是如果存储0.9这样的数字，则永远存不准确。

所以如果一个实数在MySQL中存储准确的话，会出现以下三种情况：
1. 数据真的准确，数据能在有限的存储空间里完全存储起来
2. 数据存储被截断，但是通过四舍五入依然能够将数据显示准确
3. 数据存储被截断，通过四舍五入不能将数字正确显示

### 关于decimal类型

通过前面的分析，了解了float和double类型的区别和误差来源。但是decimal类型是MySQL官方唯一指定能精确存储的类型，
也是DBA强烈推荐和金钱相关的类型都要存储为decimal类型，如果猜想decimal类型的存储格式的话，
那么以下两种可以保持数据的准确性：
1. 继续扩大存储空间，比double更大一个级别，比如128位甚至更多
2. 通过字符串化或者其他的方式特殊存储起来

这两种方式都能实现decimal精确存储，但是由于MySQL指定decimal类型最大长度为65。
在我们能测试的范围内，decimal并没有出现误差。作为MySQL官方唯一指定精确存储的decimal类型，
后续有精力再研究为什么能做到精确todo

### 如何选择float，double，decimal

结论总是放在最后，根据上面的分析：可以得出以下结论
1. 如果你要表示的浮点型数据转成二进制之后能被32位float存储，或者可以容忍截断，则使用float。
这个范围大概为要精确保存**6位数字**左右的浮点型数据，比如10分制的店铺积分可以用float存储，小商品零售价格(1000块之内)。
2. 如果你要表示的浮点型数据转成二进制之后能被64位double存储，或者可以容忍截断，
这个范围大致要精确到保存**13位数字**左右的浮点型数据。比如汽车价格,几千万的工程造价。
3. 相比double，已经满足我们大部分浮点型数据的存储精度要求，如果还要精益求精，则使用decimal定点型存储。
比如一些科学数据，精度要求很高的金钱。

### 写在最后

理论上的东西永远比不上实践，应用场景大于一切理论。选择float、或者double、或者decimal，有时候也要看场景，
比如我们可以用double存储一个小商铺的季度营业额（几千万），单独用double存储的时候没有问题，
当多个季度，多个年份算总3年内的营业额时，就会出现问题，再也算不出一个准确的答案。
所以，如果考虑情况没那么有把握的情况下，推荐使用decimal，最后，也可以通过其他手段避开这些问题，
比如存储商品价格可以使用 `乘于100`的形式存储，展示价格的时候再`除于100` 。

## 其他

### 十进制转化为二进制

十进制小数怎么转化为二进制？

十进制小数转换成二进制小数采用"乘2取整，顺序排列"法：
用2乘十进制小数，可以得出积，将积的整数部分取出，再用2乘余下的小数部分，又得到一个积，
再将积的整数部分取出，如此进行，直到积中的小数部分为零，或者达到所要求的精度为止。
然后把取出的整数部分按顺序排列起来。先取的整数作为二进制小数的高位有效位，后取的整数作为低位有效位。

如，0.6875：

![]({{site.baseurl}}/images/20220617/20220617163702.png)

整体看一个十进制数转化为二进制，如 78.6875：

![]({{site.baseurl}}/images/20220617/20220617163706.png)

### MySQL 中 小数类型

MySQL 中使用浮点数和定点数来表示小数。

浮点类型有两种，分别是单精度浮点数（FLOAT）和双精度浮点数（DOUBLE）；定点类型只有一种，就是 DECIMAL。

浮点类型和定点类型都可以用(M, D)来表示，其中M称为精度，表示总共的位数；D称为标度，表示小数的位数。

浮点数类型的取值范围为 M（1～255）和 D（1～30，且不能大于 M-2），分别表示显示宽度和小数位数。
M 和 D 在 FLOAT 和DOUBLE 中是可选的，FLOAT 和 DOUBLE 类型将被保存为硬件所支持的最大精度（M、D 只是读取时使用）。
DECIMAL 的默认 M 值为 10、D 值为 0。

DECIMAL 类型不同于 FLOAT 和 DOUBLE。DOUBLE 实际上是以字符串的形式存放的，
DECIMAL 可能的最大取值范围与 DOUBLE 相同，但是有效的取值范围由 M 和 D 决定。
如果改变 M 而固定 D，则取值范围将随 M 的变大而变大。


<br/><br/><br/><br/><br/>
## 参考资料

MySQL如何选择float, double, decimal  <http://yongxiong.leanote.com/post/mysql_float_double_decimal>

十进制转化为二进制小数 <https://baijiahao.baidu.com/s?id=1727715572385924262&wfr=spider&for=pc>

小数的二进制如何计算 <https://blog.csdn.net/qq_29055201/article/details/90725619>

MySQL FLOAT、DOUBLE、DECIMAL（小数类型） <http://c.biancheng.net/view/2424.html>

