---
layout: post
categories: MySQL
title: MySQL 主从同步之GTID同步
meta: MySQL 主从同步之GTID同步
---
* content
{:toc}

### 正文

在日常运维中，GTID带来最方便的作用就是主从复制的搭建和维护，GTID的主从模式代替了传统的利用二进制日志文件的名称和日志位置的做法， 使操作和维护更加方便。
本文的MySQL版本为：Server version: 5.7.20-18-log Percona Server (GPL), Release 18, Revision 7ce54a6deef

#### 概念先行

**什么是GTID**

每提交一个事务，当前线程都会拿到一个唯一标识符，此标识符不仅对源实例是唯一的，而且对于复制环境中所有的实例都是唯一的。
所有事务与GTID之间都是一一对应的。格式为：GTID=source_id:sequence_id，source_id为服务器的唯一标识，
通常使用服务器的server_uuid来表示，sequence_id是在事务提交时由系统顺序分配的一个序列号。
相同source_id值的事务对应的sequence_id在binlog文件中是递增且连续有序的。
可以通过`show master status` 查看当前执行过的GTID信息。如图，当前实例总共提交了132995546个事务。

![]({{site.baseurl}}/images/20210310/20210310143218.png)

**GTID集合**

GTID集合是一组全局事务表示符，在MySQL常见的几种使用方式如下：

![]({{site.baseurl}}/images/20210310/20210310143219.png)

gtid_executed：这既是一个Global级别的变量，又是一个Session级别的变量，是只读变量。Global级别的gtid_executed表示当前实例已经执行过的GTID集合。

gtid_owned：这既是一个Global级别的变量，又是一个Session级别的变量，是只读变量。Global级别的gtid_owned表示当前实例正在执行中的GTID。

gtid_purged：这是一个Global级别的变量，可动态修改。binlog可以被purge掉，gtid_purged表示当前实例中已经被purge掉的GTID集合，
gtid_purged是gtid_executed的子集。但是gtid_purged也不是可以随意修改的，必须在@@global.gtid_executed是空的情况下，才可以动态设置。

**GTID生命周期**

a. 在Master上执行一个事务，将会产生一个GTID信息保存到binlog中。

b. 将二进制信息发送到SLAVE服务器上，并存储在Relay Log中，Slave读取GTID并设置其gtid_next值为该GTID值，从而告诉Slave必须使用该GTID记录下一个事务。

c. Slave执行GTID，Slave首先验证其是否已经在自己的二进制中使用过该GTID，如果未使用过，Slave则写入该GTID，应用其事务，并写入二进制日志。

d. Slave不生成GTID，由于gtid_next不为空，SALVE不会尝试为该事务生成新的GTID，而是从gtid_next读取GTID值并写入二进制文件中，
来标识一个事务的GTID值，之后在集群中都会始终对应这个GTID值，且不会发生变化，起到“身份证”标签的作用。

#### 重要参数

* server_id
    MySQL实例的server_id，每个实例的server_id不能一样。建议使用ip地址后三段组成的数字。
* gtid_mode=ON
    开启gtid模式。
* enforce_gtid_consistency=ON
    使用GTID模式需要开启此参数，保证GTID的一致性。
* log_bin
    必须开启binlog。
* log_slave_updates=1
    决定SALVE从MASER接受到的更新且执行完之后，执行的binlog是否写到SLAVE的binlog中，建议开启。
* binlog_format=row
    强烈建议使用row模式，其它模式很容易造成主从数据不一致。
* skip_slave_start=1
    当Slave数据库启动的时候，Slave不会自动开启复制。

#### 主从搭建

* 备份

```
# mysqldump -uroot -p -h192.168.76.121 --single-transaction --master-data=2 -A > /data/mysql_bak/fullbackup.sql
```

本文采用逻辑备份方式备份数据库

* 恢复

a.从数据库执行reset master命令如图：

![]({{site.baseurl}}/images/20210310/20210310143220.png)

b.恢复备份文件

```
# mysql -uroot -p --socket=/data/mysql/3306/mysql3306.sock < /data/mysql_bak/fullbackup.sql
```

* 主从建立

在从数据库执行以下命令：
`CHANGE MASTER TO MASTER_HOST = '192.168.76.121',MASTER_PORT = 3306,MASTER_USER = 'rep',MASTER_PASSWORD = 'P@ss1234',MASTER_AUTO_POSITION = 1`

如图：

![]({{site.baseurl}}/images/20210310/20210310143221.png)

* 主从确认

从数据库执行以下命令:`show slave status\G` 如图，返回结果中Slave_IO_Running和Slave_SQL_Running都为Yes，表示从库复制正常。

![]({{site.baseurl}}/images/20210310/20210310143222.png)

#### GTID的限制

由于基于GTID的复制依赖于事务，在使用GTID时，有些MySQL特性不支持，如下：

同一个事务中不允许同时操作不同引擎的表。如图：

![]({{site.baseurl}}/images/20210310/20210310143223.png)

主从的表引擎不一致，会导致数据不一致。

基于GTID的模式，不支持create table ...select 语句。如图：

![]({{site.baseurl}}/images/20210310/20210310143224.png)

<br/><br/><br/><br/><br/>
### 参考资料



