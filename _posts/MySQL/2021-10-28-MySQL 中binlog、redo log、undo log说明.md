---
layout: post
categories: MySQL
title: MySQL 中binlog、redo log、undo log说明
meta: MySQL 中binlog、redo log、undo log说明
---
* content
{:toc}

## 正文

日志是 mysql 数据库的重要组成部分，记录着数据库运行期间各种状态信息。
mysql日志主要包括错误日志、查询日志、慢查询日志、事务日志、二进制日志几大类。

作为开发，我们重点需要关注的是二进制日志( binlog )和事务日志(包括redo log 和 undo log )，本文接下来会详细介绍这三种日志。

### bin log

binlog 用于记录数据库执行的写入性操作(不包括查询)信息，以二进制的形式保存在磁盘中。binlog 是 mysql的逻辑日志，
并且由 Server 层进行记录，使用任何存储引擎的 mysql 数据库都会记录 binlog 日志。

逻辑日志：可以简单理解为记录的就是sql语句 。

物理日志：mysql 数据最终是保存在数据页中的，物理日志记录的就是数据页变更 。

binlog 是通过追加的方式进行写入的，可以通过max_binlog_size 参数设置每个 binlog文件的大小，
当文件大小达到给定值之后，会生成新的文件来保存日志。

#### binlog使用场景

在实际应用中， binlog 的主要使用场景有两个，分别是 主从复制 和 数据恢复 。

1. **主从复制** ：在 Master 端开启 binlog ，然后将 binlog发送到各个 Slave 端， Slave 端重放 binlog 从而达到主从数据一致。
2. **数据恢复** ：通过使用 mysqlbinlog 工具来恢复数据。

#### binlog刷盘时机

对于 InnoDB 存储引擎而言，只有在事务提交时才会记录biglog ，此时记录还在内存中，那么 biglog是什么时候刷到磁盘中的呢？
mysql 通过 sync_binlog 参数控制 biglog 的刷盘时机，取值范围是 0-N：

* 0：不去强制要求，由系统自行判断何时写入磁盘；
* 1：每次 commit 的时候都要将 binlog 写入磁盘；
* N：每N个事务，才会将 binlog 写入磁盘。

从上面可以看出， sync_binlog 最安全的是设置是 1 ，这也是MySQL 5.7.7之后版本的默认值。
但是设置一个大一些的值可以提升数据库性能，因此实际情况下也可以将值适当调大，牺牲一定的一致性来获取更好的性能。

#### binlog日志格式

binlog 日志有三种格式，分别为 STATMENT 、 ROW 和 MIXED。

    在 MySQL 5.7.7 之前，默认的格式是 STATEMENT ， MySQL 5.7.7 之后，默认值是 ROW。日志格式通过 binlog-format 指定。

* STATMENT：基于SQL 语句的复制( statement-based replication, SBR )，每一条会修改数据的sql语句会记录到binlog 中  。

优点：不需要记录每一行的变化，减少了 binlog 日志量，节约了  IO  , 从而提高了性能；

缺点：在某些情况下会导致主从数据不一致，比如执行sysdate() 、  slepp()  等 。

* ROW：基于行的复制(row-based replication, RBR )，不记录每条sql语句的上下文信息，仅需记录哪条数据被修改了 。

优点：不会出现某些特定情况下的存储过程、或function、或trigger的调用和触发无法被正确复制的问题 ；

缺点：会产生大量的日志，尤其是` alter table ` 的时候会让日志暴涨

* MIXED：基于STATMENT 和 ROW 两种模式的混合复制(mixed-based replication, MBR )，一般的复制使用STATEMENT 模式保存 binlog ，
对于 STATEMENT 模式无法复制的操作使用 ROW 模式保存 binlog

### redo log

#### 为什么需要redo log

我们都知道，事务的四大特性里面有一个是 持久性 ，具体来说就是只要事务提交成功，
那么对数据库做的修改就被永久保存下来了，不可能因为任何原因再回到原来的状态 。

那么 mysql是如何保证一致性的呢？

最简单的做法是在每次事务提交的时候，将该事务涉及修改的数据页全部刷新到磁盘中。
但是这么做会有严重的性能问题，主要体现在两个方面：
1. 因为 Innodb 是以 页 为单位进行磁盘交互的，而一个事务很可能只修改一个数据页里面的几个字节，
这个时候将完整的数据页刷到磁盘的话，太浪费资源了！
2. 一个事务可能涉及修改多个数据页，并且这些数据页在物理上并不连续，使用随机IO写入性能太差！

因此 mysql 设计了 redo log ， **具体来说就是只记录事务对数据页做了哪些修改**，
这样就能完美地解决性能问题了(相对而言文件更小并且是顺序IO)。

#### redo log基本概念

redo log 包括两部分：一个是内存中的日志缓冲( redo log buffer )，另一个是磁盘上的日志文件( redo logfile)。

mysql 每执行一条 DML 语句，先将记录写入 redo log buffer，后续某个时间点再一次性将多个操作记录写到 redo log file。
这种 先写日志，再写磁盘 的技术就是 MySQL里经常说到的 WAL(Write-Ahead Logging) 技术。

在计算机操作系统中，用户空间( user space )下的缓冲区数据一般情况下是无法直接写入磁盘的，
中间必须经过操作系统内核空间( kernel space )缓冲区( OS Buffer )。

因此， redo log buffer 写入 redo logfile 实际上是先写入 OS Buffer ，然后再通过系统调用 fsync() 将其刷到 redo log file中，过程如下：

![]({{site.baseurl}}/images/20211028/20211028154825.jpg)

mysql 支持三种将 redo log buffer 写入 redo log file 的时机，可以通过 innodb_flush_log_at_trx_commit 参数配置，各参数值含义如下：

![]({{site.baseurl}}/images/20211028/20211028154827.jpg)

![]({{site.baseurl}}/images/20211028/20211028154829.jpg)

#### redo log记录形式

前面说过， redo log 实际上记录数据页的变更，而这种变更记录是没必要全部保存，
因此 redo log实现上采用了大小固定，循环写入的方式，当写到结尾时，会回到开头循环写日志。如下图：

![]({{site.baseurl}}/images/20211028/20211028154831.jpg)

同时我们很容易得知， 在innodb中，既有redo log 需要刷盘，还有 数据页 也需要刷盘， 
redo log存在的意义主要就是降低对 数据页 刷盘的要求 ** 。

在上图中， write pos 表示 redo log 当前记录的 LSN (逻辑序列号)位置， 
check point 表示 数据页更改记录 刷盘后对应 redo log 所处的 LSN(逻辑序列号)位置。

write pos 到 check point 之间的部分是 redo log 空着的部分，
用于记录新的记录；check point 到 write pos 之间是 redo log 待落盘的数据页更改记录。
当 write pos追上check point 时，会先推动 check point 向前移动，空出位置再记录新的日志。

启动 innodb 的时候，不管上次是正常关闭还是异常关闭，总是会进行恢复操作。
因为 redo log记录的是数据页的物理变化，因此恢复的时候速度比逻辑日志(如 binlog )要快很多。

重启innodb 时，首先会检查磁盘中数据页的 LSN ，如果数据页的LSN 小于日志中的 LSN ，则会从 checkpoint 开始恢复。

还有一种情况，在宕机前正处于checkpoint 的刷盘过程，且数据页的刷盘进度超过了日志页的刷盘进度，
此时会出现数据页中记录的 LSN 大于日志中的 LSN，这时超出日志进度的部分将不会重做，因为这本身就表示已经做过的事情，无需再重做。

#### redo log与binlog区别

![]({{site.baseurl}}/images/20211028/20211028154833.jpg)

由 binlog 和 redo log 的区别可知：binlog 日志只用于归档，只依靠 binlog 是没有 crash-safe 能力的。

但只有 redo log 也不行，因为 redo log 是 InnoDB特有的，且日志上的记录落盘后会被覆盖掉。
因此需要 binlog和 redo log二者同时记录，才能保证当数据库发生宕机重启时，数据不会丢失。

### undo log

数据库事务四大特性中有一个是 原子性 ，具体来说就是 原子性是指对数据库的一系列操作，
要么全部成功，要么全部失败，不可能出现部分成功的情况。

实际上， 原子性 底层就是通过 undo log 实现的。undo log主要记录了数据的逻辑变化，
比如一条 INSERT 语句，对应一条DELETE 的 undo log ，对于每个 UPDATE 语句，
对应一条相反的 UPDATE 的 undo log ，这样在发生错误时，就能回滚到事务之前的数据状态。

同时， undo log 也是 MVCC(多版本并发控制)实现的关键。

## 图解MySQL为什么需要binlog、redo log和undo log

全文建立在MySQL的存储引擎为InnoDB的基础上

先看一条SQL如何入库的：

![]({{site.baseurl}}/images/20220909/20220909105451.gif)

这是一条很简单的更新SQL，从MySQL服务端接收到SQL到落盘，先后经过了MySQL Server层和InnoDB存储引擎。

1. Server层就像一个产品经理，分析客户的需求，并给出实现需求的方案。
2. InnoDB就像一个基层程序员，实现产品经理给出的具体方案。

在MySQL”分析需求，实现方案“的过程中，还夹杂着内存操作和磁盘操作，以及记录各种日志。

他们到底有什么用处？他们之间到底怎么配合的？MySQL又为什么要分层呢？InnoDB里面的那一块Buffer Pool又是什么？

我们慢慢分析。

### 分层结构

MySQL为什么要分为Server层和存储引擎两层呢？

这个问题官方也没有给出明确的答案，但是也不难猜，简单来说就是为了“解耦”。

Server层和存储引擎各司其职，分工明确，用户可以根据不同的需求去使用合适的存储引擎，多好的设计，对不对？

后来的发展也验证了“分层设计”的优越性：

MySQL最初搭载的存储引擎是自研的只支持简单查询的MyISAM的前身ISAM，后来与Sleepycat合作研发了Berkeley DB引擎，支持了事务。

江山代有才人出，技术后浪推前浪，MySQL在持续的升级着自己的存储引擎的过程中，遇到了横空出世的InnoDB，InnoDB的功能强大让MySQL倍感压力。

自己的存储引擎打不过InnoDB怎么办？

打不过就加入！

MySQL选择了和InnoDB合作。正是因为MySQL存储引擎的插件化设计，两个公司合作的非常顺利，MySQL也在合作后不久就发布了正式支持nnoDB的4.0版本以及经典的4.1版本。

MySQL兼并天下模式也成为MySQL走向繁荣的一个重要因素。这能让MySQL长久地保持着极强竞争力。

时至今日，MySQL依然占据着极高数据库市场份额，仅次于王牌数据库Oracle。

### Buffer Pool

在InnoDB里，有一块非常重要的结构——Buffer Pool。

Buffer Pool是个什么东西呢？

Buffer Pool就是一块用于缓存MySQL磁盘数据的内存空间。

为什么要缓存MySQL磁盘数据呢？

我们通过一个例子说明，我们先假设没有Buffer Pool，user表里面只有一条记录，记录的age = 1，假设需要执行三条SQL：

1. 事务A：update user set age = 2
2. 事务B：update user set age = 3
3. 事务C：update user set age = 4

如果没有Buffer Pool，那执行就是这样的：

![]({{site.baseurl}}/images/20220909/20220909105551.gif)

从图上可以看出，每次更新都需要从磁盘拿数据（1次IO），修改完了需要刷到磁盘（1次IO），
也就是每次更新都需要2次磁盘IO。三次更新需要6次磁盘IO。

而有了Buffer Pool，执行就成了这样：

![]({{site.baseurl}}/images/20220909/20220909105553.gif)

从图上可以看出，只需要在第一次执行的时候将数据从磁盘拿到Buffer Pool（1次IO），
第三次执行完将数据刷回磁盘（1次IO），整个过程只需要2次磁盘IO，比没有Buffer Pool节省了4次磁盘IO的时间。

当然，Buffer Pool真正的运转流程没有这么简单，具体实现细节和优化技巧还有很多，由于篇幅有限，本文不做详细描述。

我想表达的是：Buffer Pool就是将磁盘IO转换成了内存操作，节省了时间，提高了效率。

Buffer Pool是提高了效率没错，但是出现了一个问题，Buffer Pool是基于内存的，而只要一断电，内存里面的数据就会全部丢失。

如果断电的时候Buffer Pool的数据还没来得及刷到磁盘，那么这些数据不就丢失了吗？

还是上面的那个例子，如果三个事务执行完毕，在age = 4还没有刷到磁盘的时候，突然断电，数据就全部丢掉了：

![]({{site.baseurl}}/images/20220909/20220909105555.gif)

试想一下，如果这些丢失的数据是核心的用户交易数据，那用户能接受吗？

答案是否定的。

那InnoDB是如何做到数据不会丢失的呢？

今天的第一个日志——redo log登场了。

### 恢复 - redo log

顾名思义，redo是重做的意思，redo log就是重做日志的意思。

redo log是如何保证数据不会丢失的呢？

就是在修改之后，先将修改后的值记录到磁盘上的redo log中，就算突然断电了，Buffer Pool中的数据全部丢失了，
来电的时候也可以根据redo log恢复Buffer Pool，这样既利用到了Buffer Pool的内存高效性，也保证了数据不会丢失。

我们通过一个例子说明，我们先假设没有Buffer Pool，user表里面只有一条记录，记录的age = 1，假设需要执行一条SQL：

1、事务A：update user set age = 2

执行过程如下：

![]({{site.baseurl}}/images/20220909/20220909105557.gif)

如上图，有了redo log之后，将age修改成2之后，马上将age = 2写到redo log里面，如果这个时候突然断电内存数据丢失，
在来电的时候，可以将redo log里面的数据读出来恢复数据，用这样的方式保证了数据不会丢失。

你可能会问，redo log文件也在磁盘上，数据文件也在磁盘上，都是磁盘操作，何必多此一举？为什么不直接将修改的数据写到数据文件里面去呢？

傻瓜，因为redo log是磁盘顺序写，数据刷盘是磁盘随机写，磁盘的顺序写比随机写高效的多啊。

这种先预写日志后面再将数据刷盘的机制，有一个高大上的专业名词——WAL（Write-ahead logging），翻译成中文就是预写式日志。

虽然磁盘顺序写已经很高效了，但是和内存操作还是有一定的差距。

那么，有没有办法进一步优化一下呢？

答案是可以。那就是给redo log也加一个内存buffer，也就是redo log buffer，用这种套娃式的方法进一步提高效率。

![]({{site.baseurl}}/images/20220909/20220909105559.jpg)

redo log buffer具体是怎么配合刷盘呢？

在回答这个问题之前之前，我们先来捋一下MySQL服务端和操作系统的关系：

MySQL服务端是一个进程，它运行于操作系统之上。也就是说，操作系统挂了MySQL一定挂了，但是MySQL挂了操作系统不一定挂。

所以MySQL挂了有两种情况：

1. MySQL挂了，操作系统也挂了，也就是常说的服务器宕机了。这种情况Buffer Pool里面的数据会全部丢失，操作系统的os cache里面的数据也会丢失。
2. MySQL挂了，操作系统没有挂。这种情况Buffer Pool里面的数据会全部丢失，操作系统的os cache里面的数据不会丢失。

OK，了解了MySQL服务端和操作系统的关系之后，再来看redo log的落盘机制。redo log的刷盘机制由参数innodb_flush_log_at_trx_commit控制，这个参数有3个值可以设置：

1. innodb_flush_log_at_trx_commit = 1：实时写，实时刷
2. innodb_flush_log_at_trx_commit = 0：延迟写，延迟刷
3. innodb_flush_log_at_trx_commit = 2：实时写，延迟刷

写可以理解成写到操作系统的缓存（os cache），刷可以理解成把操作系统里面的缓存刷到磁盘。

这三种策略的区别，我们分开讨论：

**innodb_flush_log_at_trx_commit = 1：实时写，实时刷**

这种策略会在每次事务提交之前，每次都会将数据从redo log刷到磁盘中去，理论上只要磁盘不出问题，数据就不会丢失。

总结来说，这种策略效率最低，但是丢数据风险也最低。

![]({{site.baseurl}}/images/20220909/20220909105561.gif)

**innodb_flush_log_at_trx_commit = 0：延迟写，延迟刷**

这种策略在事务提交时，只会把数据写到redo log buffer中，然后让后台线程定时去将redo log buffer里面的数据刷到磁盘。

这种策略是最高效的，但是我们都知道，定时任务是有间隙的，但是如果事务提交后，后台线程没来得及将redo log刷到磁盘，
这个时候不管是MySQL进程挂了还是操作系统挂了，这一部分数据都会丢失。

总结来说这种策略效率最高，丢数据的风险也最高。

![]({{site.baseurl}}/images/20220909/20220909105563.gif)

**innodb_flush_log_at_trx_commit = 2：实时写，延迟刷**

这种策略在事务提交之前会把redo log写到os cache中，但并不会实时地将redo log刷到磁盘，而是会每秒执行一次刷新磁盘操作。

这种情况下如果MySQL进程挂了，操作系统没挂的话，操作系统还是会将os cache刷到磁盘，数据不会丢失，如下图：

![]({{site.baseurl}}/images/20220909/20220909105565.gif)

但如果MySQL所在的服务器挂掉了，也就是操作系统都挂了，那么os cache也会被清空，数据还是会丢失。如下图：

![]({{site.baseurl}}/images/20220909/20220909105567.gif)

所以，这种redo log刷盘策略是上面两种策略的折中策略，效率比较高，丢失数据的风险比较低，绝大多情况下都推荐这种策略。

总结一下，redo log的作用是用于恢复数据，写redo log的过程是磁盘顺序写，
有三种刷盘策略，有innodb_flush_log_at_trx_commit 参数控制，推荐设置成2。

### 回滚 - undo log

我们都知道，InnoDB是支持事务的，而事务是可以回滚的。

假如一个事务将age=1修改成了age=2，在事务还没有提交的时候，后台线程已经将age=2刷入了磁盘。
这个时候，不管是内存还是磁盘上，age都变成了2，如果事务要回滚，找不到修改之前的age=1，无法回滚了。

![]({{site.baseurl}}/images/20220909/20220909105569.mp4)

那怎么办呢？

很简单，把修改之前的age=1存起来，回滚的时候根据存起来的age=1回滚就行了。

MySQL确实是这么干的！这个记录修改之前的数据的过程，叫做记录undo log。undo翻译成中文是撤销、回滚的意思，undo log的主要作用也就是回滚数据。

如何回滚呢？看下面这个图：

![]({{site.baseurl}}/images/20220909/20220909105571.mp4)

MySQL在将age = 1修改成age = 2之前，先将age = 1存到undo log里面去，这样需要回滚的时候，可以将undo log里面的age = 1读出来回滚。

需要注意的是，undo log默认存在全局表空间里面，你可以简单的理解成undo log也是记录在一个MySQL的表里面，
插入一条undo log和插入一条普通数据是类似。也就是说，写undo log的过程中同样也是要写入redo log的。

### 归档 - binlog

undo log记录的是修改之前的数据，提供回滚的能力。

redo log记录的是修改之后的数据，提供了崩溃恢复的能力。

那binlog是干什么的呢？

binlog记录的是修改之后的数据，用于归档。

和redo log日志类似，binlog也有着自己的刷盘策略，通过sync_binlog参数控制：

* sync_binlog = 0 ：每次提交事务前将binlog写入os cache，由操作系统控制什么时候刷到磁盘
* sync_binlog =1 ：采用同步写磁盘的方式来写binlog，不使用os cache来写binlog
* sync_binlog = N ：当每进行n次事务提交之后，调用一次fsync将os cache中的binlog强制刷到磁盘

那么问题来了，binlog和redo log都是记录的修改之后的值，这两者有什么区别呢？有redo log为什么还需要binlog呢？

首先看两者的一些区别：

* binlog是逻辑日志，记录的是对哪一个表的哪一行做了什么修改；redo log是物理日志，
记录的是对哪个数据页中的哪个记录做了什么修改，如果你还不了解数据页，你可以理解成对磁盘上的哪个数据做了修改。
* binlog是追加写；redo log是循环写，日志文件有固定大小，会覆盖之前的数据。
* binlog是Server层的日志；redo log是InnoDB的日志。如果不使用InnoDB引擎，是没有redo log的。

但说实话，我觉得这些区别并不是redo log不能取代binlog的原因，MySQL官方完全可以调整redo log让他兼并binlog的能力，但他没有这么做，为什么呢？

我认为不用redo log取代binlog最大的原因是“没必要”。

为什么这么说呢？

第一点，binlog的生态已经建立起来。MySQL高可用主要就是依赖binlog复制，还有很多公司的数据分析系统和数据处理系统，也都是依赖的binlog。取代binlog去改变一个生态费力了不讨好。

第二点，binlog并不是MySQL的瓶颈，花时间在没有瓶颈的地方没必要。

### 总结

1. Buffer Pool是MySQL进程管理的一块内存空间，有减少磁盘IO次数的作用。
2. redo log是InnoDB存储引擎的一种日志，主要作用是崩溃恢复，有三种刷盘策略，有innodb_flush_log_at_trx_commit 参数控制，推荐设置成2。
3. undo log是InnoDB存储引擎的一种日志，主要作用是回滚。
4. binlog是MySQL Server层的一种日志，主要作用是归档。
5. MySQL挂了有两种情况：操作系统挂了MySQL进程跟着挂了；操作系统没挂，但是MySQL进程挂了。

最后，再用一张图总结一下全文的知识点：

![]({{site.baseurl}}/images/20220909/20220909105573.jpg)

### 写在最后

这篇文章写在一年之前，本来觉得是一篇水文没想要发，最近无聊修改了一下发了出来，希望能够用动图的形式帮助到MySQL基础不太好的朋友，大神忽略就好。

需要强调的一点是，由于作者水平有限，本文只是浅显的从无到有地阐述了MySQL几种日志的大致作用，过程中省略了很多细节，
比如Buffer Pool的实现细节，比如undo log和MVCC的关系，比如binlog buffer、change buffer的存在，比如redo log的两阶段提交。

如果您有任何问题，我们可以探讨，如果您在文中发现错误，还望您指出，万分感谢！

好了，今天的文章就到这里了。

感谢你的阅读！我是CoderW，我们下期再见。

### 参考资料

* 《MySQL实战45讲》
* 《从根儿上理解MySQL》
* 《MySQL技术内幕—InnoDB存储引擎》第2版







<br/><br/><br/><br/><br/>
## 参考资料

一文聊透binlog、redo log、undo log，深度好文 <https://mp.weixin.qq.com/s/1zMTlmzoJ9zi3lhrBCj7ew>

必须了解的mysql三大日志-binlog、redo log和undo log  <https://juejin.cn/post/6860252224930070536>

3000帧动画图解MySQL为什么需要binlog、redo log和undo log <https://mp.weixin.qq.com/s/2Y-9noKZKzePqYjr5GikeA>

3000帧动画图解MySQL为什么需要binlog、redo log和undo log <https://zhuanlan.zhihu.com/p/532295368>


