---
layout: post
categories: MySQL
title: MySQL 各JOIN区别总结
meta: MySQL开发中LEFT JOIN、INNER JOIN、RIGHT JOIN的区别是什么呢，下面讲讲。
---
* content
{:toc}

## 正文

也是因为搬砖过程中，一个一个bug的出现，让我在反思怎样减少bug以提高效率。但是经验不总结就不是可用经验，所以要写下来。
这次是因为用了INNER JOIN导致查询数据丢失，初以为数据表丢了呢，结果最后追查到join这里，本应该用LEFT JOIN的。
那么LEFT JOIN与INNER JOIN有什么区别呢？

LEFT JOIN正如其名——左关联查询，以左表为根据表，左表数据肯定存在，右表数据可能不存在，左对右是一对多查询；
左表为空，则查询结果为空；左表不为空，右表为空，则查询结果存在，但右表返回结果为null。查询时用左表驱动右表。

INNER JOIN正如其名——内关联查询，并不以谁为基础，它只显示符合条件的记录。 

RIGHT JOIN 与 LEFT JOIN 同理。

关键是使用的时候，要考虑清楚哪个表可能没数据，然后选择合适的Join。

![]({{site.baseurl}}/images/20230302/20230302211180.jpg)

LEFT JOIN(左联接) 返回包括左表中的所有记录和右表中联结字段相等的记录

RIGHT JOIN(右联接) 返回包括右表中的所有记录和左表中联结字段相等的记录

INNER JOIN(等值连接) 只返回两个表中联结字段相等的行

![]({{site.baseurl}}/images/20210128/20210128184392.png)

FULL OUTER JOIN

LEFT OUTER JOIN

RIGHT OUTER JOIN

UNION 与 UNION ALL

CROSS JOIN 

## 实例

通过文氏图 Venn diagrams 解释了SQL的Join。

假设我们有两张表。Table A 是左边的表。Table B 是右边的表。其各有四条记录，
其中有两条记录name是相同的，如下所示，让我们看看不同JOIN的不同：

![]({{site.baseurl}}/images/20230302/20230302211120.png)

1、INNER JOIN

`SELECT * FROM TableA INNER JOIN TableB ON TableA.name = TableB.name`

![]({{site.baseurl}}/images/20230302/20230302211130.png)

2、`FULL [OUTER] JOIN`

(1) `SELECT * FROM TableA FULL OUTER JOIN TableB ON TableA.name = TableB.name`

![]({{site.baseurl}}/images/20230302/20230302211140.png)

(2) `SELECT * FROM TableA FULL OUTER JOIN TableB ON TableA.name = TableB.name WHERE TableA.id is null OR TableB.id is null`

![]({{site.baseurl}}/images/20230302/20230302211143.png)

3、`LEFT [OUTER] JOIN`

(1) `SELECT * FROM TableA LEFT OUTER JOIN TableB ON TableA.name = TableB.name`

(2) `SELECT * FROM TableA LEFT OUTER JOIN TableB ON TableA.name = TableB.name WHERE TableB.id is null`

![]({{site.baseurl}}/images/20230302/20230302211146.png)

4、`RIGHT [OUTER] JOIN`

`RIGHT [OUTER] JOIN` 是后面的表为基础，与 `LEFT [OUTER] JOIN` 用法类似。这里不介绍了。

5、UNION 与 UNION ALL

UNION 操作符用于合并两个或多个 SELECT 语句的结果集。
请注意，UNION 内部的 SELECT 语句必须拥有相同数量的列。列也必须拥有相似的数据类型。
同时，每条 SELECT 语句中的列的顺序必须相同。UNION 只选取记录，而UNION ALL会列出所有记录。

(1) `SELECT name FROM TableA UNION SELECT name FROM TableB`

![]({{site.baseurl}}/images/20230302/20230302211150.png)

选取不同值

(2) `SELECT name FROM TableA UNION ALL SELECT name FROM TableB`

![]({{site.baseurl}}/images/20230302/20230302211160.png)

全部列出来

(3) 注意：`SELECT * FROM TableA UNION SELECT * FROM TableB`

![]({{site.baseurl}}/images/20230302/20230302211170.png)

由于 id 1 Pirate   与 id 2 Pirate 并不相同，不合并

6、CROSS JOIN 

还需要注册的是我们还有一个是“交差集” CROSS JOIN , 这种Join没有办法用文式图表示，
因为其就是把表A和表B的数据进行一个 N * M 的组合，即笛卡尔积。
表达式如下：`SELECT * FROM TableA CROSS JOIN TableB`

这个笛卡尔乘积会产生 4 x 4 = 16 条记录，一般来说，我们很少用到这个语法。但是我们得小心，
如果不是使用嵌套的select语句，一般系统都会产生笛卡尔乘积然再做过滤。这是对于性能来说是非常危险的，尤其是表很大的时候。



<br/><br/><br/><br/><br/>
## 参考资料

图解SQL的inner join、left join、right join、full outer join、union、union all的区别 <https://www.cnblogs.com/logon/p/3748020.html>

Visual Representation of SQL Joins <https://www.codeproject.com/Articles/33052/Visual-Representation-of-SQL-Joins>

一张图告诉你SQL使用inner join，left join 等 <https://blog.csdn.net/weixin_41796956/article/details/85044152>

sql之left join、right join、inner join的区别 <https://www.cnblogs.com/pcjim/articles/799302.html>
