---
layout: post
categories: MySQL
title: MySQL not exists用法
meta: MySQL not exists用法
---
* content
{:toc}

### 正文

not exists 是说查询where条件中不存在的，形如：
```
select * from A where not exists(select 1 from B where cc=A.cc)
```

是说查找A表中cc字段在B表中不存在的记录。

#### in和exists

in是把外表和内表作hash连接，而exists是对外表作loop循环，每次loop循环再对内表进行查询。
如果查询的两个表大小相当，那么用in和exists差别不大；如果两个表中一个较小一个较大，则子查询表大的用exists，子查询表小的用in。

例如：表A(小表)，表B(大表)
```
select * from A where cc in(select cc from B)　　-->效率低，用到了A表上cc列的索引； 

select * from A where exists(select cc from B where cc=A.cc)　　-->效率高，用到了B表上cc列的索引。
```

```
select * from B where cc in(select cc from A)　　-->效率高，用到了B表上cc列的索引 

select * from B where exists(select cc from A where cc=B.cc)　　-->效率低，用到了A表上cc列的索引。
```

#### not in 和not exists

not in 逻辑上不完全等同于not exists，如果你误用了not in，小心你的程序存在致命的BUG，请看下面的例子：
```
create table #t1(c1 int,c2 int);

create table #t2(c1 int,c2 int);

insert into #t1 values(1,2);

insert into #t1 values(1,3);

insert into #t2 values(1,2);

insert into #t2 values(1,null);

 

select * from #t1 where c2 not in(select c2 from #t2);　　-->执行结果：无 

select * from #t1 where not exists(select 1 from #t2 where #t2.c2=#t1.c2)　　-->执行结果：1　　3
```

正如所看到的，not in出现了不期望的结果集，存在逻辑错误。如果看一下上述两个select 语句的执行计划，也会不同，后者使用了hash_aj，
所以，请尽量不要使用not in(它会调用子查询)，而尽量使用not exists（它会调用关联子查询）。
如果子查询中返回的任意一条记录含有空值，则查询将不返回任何记录。如果子查询字段有非空限制，这时可以使用not in，
并且可以通过提示让它用hasg_aj或merge_aj连接。

如果查询语句使用了not in，那么对内外表都进行全表扫描，没有用到索引；而not exists的子查询依然能用到表上的索引。
所以无论哪个表大，用not exists都比not in 要快。

#### in 与 = 的区别

```
select name from student where name in('zhang','wang','zhao');

select name from student where name='zhang' or name='wang' or name='zhao'
```

查询结果是相同的。

#### 查询过程分析

1.EXISTS的执行流程 
```
select * from t1 where exists ( select null from t2 where y = x ) 
```

可以理解为: 
```
for x in ( select * from t1 ) loop 

if ( exists ( select null from t2 where y = x.x ) then 
OUTPUT THE RECORD 
end if 
end loop 
```

对于in 和 exists的性能区别: 

如果子查询得出的结果集记录较少，主查询中的表较大且又有索引时应该用in,反之如果外层的主查询记录较少，子查询中的表大，又有索引时使用exists。 

其实我们区分in和exists主要是造成了驱动顺序的改变（这是性能变化的关键），如果是exists，那么以外层表为驱动表，先被访问，
如果是IN，那么先执行子查询，所以我们会以驱动表的快速返回为目标，那么就会考虑到索引及结果集的关系了 


另外IN时不对NULL进行处理 

如： 
```
select 1 from dual where null in (0,1,2,null) 为空 
```

2.NOT IN 与NOT EXISTS: 

NOT EXISTS的执行流程 
```
select ..... from rollup R  where not exists ( select 'Found' from title T where R.source_id = T.Title_ID); 
```

可以理解为: 
```
for x in ( select * from rollup ) loop 
if ( not exists ( that query ) ) then 
OUTPUT 
end if; 
end loop; 
```

注意:NOT EXISTS 与 NOT IN 不能完全互相替换，看具体的需求。如果选择的列可以为空，则不能被替换。 


例如下面语句，看他们的区别： 
```
select x,y from t; 
```

查询x和y数据如下：
```
x y 
------ ------ 
1 3 
3 1 
1 2 
1 1 
3 1 
5 
```

使用not in 和not exists查询结果如下：
```
select * from t where x not in (select y from t t2 ) ;
```

查询无结果：no rows

```
select * from t where not exists (select null from t t2 where t2.y=t.x ) ;
```

查询结果为：
```
x y 
------ ------ 
5 NULL 
```

所以要具体需求来决定 


对于not in 和 not exists的性能区别： 

not in 只有当子查询中，select 关键字后的字段有not null约束或者有这种暗示时用not in,另外如果主查询中表大，
子查询中的表小但是记录多，则应当使用not in,并使用anti hash join. 

如果主查询表中记录少，子查询表中记录多，并有索引，可以使用not exists,另外not in最好也可以用/*+ HASH_AJ */或者外连接+is null 
NOT IN 在基于成本的应用中较好 


比如: 
```
select ..... 
from rollup R 
where not exists ( select 'Found' from title T 
where R.source_id = T.Title_ID); 
```

改成（佳） 

```
select ...... 
from title T, rollup R 
where R.source_id = T.Title_id(+) 
and T.Title_id is null; 
```

或者（佳） 
```
sql> select /*+ HASH_AJ */ ... 
from rollup R 
where ource_id NOT IN ( select ource_id 
from title T 
where ource_id IS NOT NULL ) 
```

讨论IN和EXISTS。 
```
select * from t1 where x in ( select y from t2 ) 
```

事实上可以理解为： 
```
select * 
from t1, ( select distinct y from t2 ) t2 
where t1.x = t2.y; 
```

——如果你有一定的SQL优化经验，从这句很自然的可以想到t2绝对不能是个大表，因为需要对t2进行全表的“唯一排序”，
如果t2很大这个排序的性能是 不可忍受的。但是t1可以很大，为什么呢？最通俗的理解就是因为t1.x=t2.y可以走索引。但这并不是一个很好的解释。
试想，如果t1.x和t2.y 都有索引，我们知道索引是种有序的结构，因此t1和t2之间最佳的方案是走merge join。
另外，如果t2.y上有索引，对t2的排序性能也有很大提高。 
```
select * from t1 where exists ( select null from t2 where y = x ) 
```

可以理解为： 
```
for x in ( select * from t1 ) 
loop 
if ( exists ( select null from t2 where y = x.x ) 
then 
OUTPUT THE RECORD! 
end if 
end loop 
```

——这个更容易理解，t1永远是个表扫描！因此t1绝对不能是个大表，而t2可以很大，因为y=x.x可以走t2.y的索引。 
综合以上对IN/EXISTS的讨论，我们可以得出一个基本通用的结论：IN适合于外表大而内表小的情况；EXISTS适合于外表小而内表大的情况。 

我们要根据实际的情况做相应的优化，不能绝对的说谁的效率高谁的效率低，所有的事都是相对的。

<br/><br/><br/><br/><br/>
### 参考资料

浅谈sql中的in与not in,exists与not exists的区别以及性能分析 <https://blog.csdn.net/baidu_37107022/article/details/77278381>

