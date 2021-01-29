---
layout: post
categories: MySQL
title: MySQL leftJoin、innerJoin、rightJoin区别总结
meta: MySQL开发中leftJoin、innerJoin、rightJoin的区别是什么呢，下面讲讲。
---
* content
{:toc}

### 正文

也是因为搬砖过程中，一个一个bug的出现，让我在反思怎样减少bug以提高效率。但是经验不总结就不是可用经验，所以要写下来。
这次是因为用了innerJoin导致查询数据丢失，初以为数据表丢了呢，结果最后追查到join这里，本应该用leftJoin的。
那么leftJoin与innerJoin有什么区别呢？

leftJoin正如其名——左关联查询，以左表为根据表，左表数据肯定存在，右表数据可能不存在，左对右是一对多查询；
左表为空，则查询结果为空；左表不为空，右表为空，则查询结果存在，但右表返回结果为null。查询时用左表驱动右表。

innerJoin正如其名——内关联查询，以右表为根据表，右表数据肯定存在，左表数据可能不存在，右对左是一对多查询。
右表数据必须存在，不然查询结果为空；右表数据存在，但左表为空，则查询结果为空。
查询时用右表驱动左表，如果右表数据少，左表数据多，用innerJoin可以提高查询效率和速度。 

rightJoin与leftJoin同理。

关键是使用的时候，要考虑清楚哪个表可能没数据，然后选择合适的Join。

![]({{site.baseurl}}/images/20210128/20210128184390.png)

left join(左联接) 返回包括左表中的所有记录和右表中联结字段相等的记录

right join(右联接) 返回包括右表中的所有记录和左表中联结字段相等的记录

inner join(等值连接) 只返回两个表中联结字段相等的行

![]({{site.baseurl}}/images/20210128/20210128184392.png)

<br/><br/><br/><br/><br/>
### 参考资料：

图解SQL的inner join、left join、right join、full outer join、union、union all的区别 <https://www.cnblogs.com/logon/p/3748020.html>

Visual Representation of SQL Joins <https://www.codeproject.com/Articles/33052/Visual-Representation-of-SQL-Joins>

一张图告诉你SQL使用inner join，left join 等 <https://blog.csdn.net/weixin_41796956/article/details/85044152>

sql之left join、right join、inner join的区别 <https://www.cnblogs.com/pcjim/articles/799302.html>
