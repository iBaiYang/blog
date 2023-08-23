---
layout: post
categories: MySQL
title: MySQL only_full_group_by错误处理
meta: MySQL only_full_group_by错误处理
---
* content
{:toc}

## 正文

在MySQL的使用中，如select查询和create创建表时，可能会报错：
```
[Err] 1055 - Expression #1 of ORDER BY clause is not in GROUP BY clause 
and contains nonaggregated column 'information_schema.PROFILING.SEQ' 
which is not functionally dependent on columns in GROUP BY clause; 
this is incompatible with sql_mode=only_full_group_by
```

翻译过来是： ORDER BY子句不在GROUP BY子句中，包含非聚合列 `information_schema.PROFILING.SEQ`，
它在功能上不依赖于GROUP BY子句中的列，这与sql模式 `only_full_group_by` 不兼容。

大意是说 ORDER BY 的 字段未在 GROUP BY 中出现一类的，这是MySQL严格模式，我们修改下模式就好了。

如果MySQL安装在docker中，先进入相应docker容器：
> docker exec -it server-mysql /bin/bash

进入mysql服务：
> mysql -u root -p

输入密码。

查看MySQL模式：
> show variables like 'sql_mode';

输出：
```
+---------------+-------------------------------------------------------------------------------------------------------------------------------------------+
| Variable_name | Value                                                                                                                                     |
+---------------+-------------------------------------------------------------------------------------------------------------------------------------------+
| sql_mode      | ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION |
+---------------+-------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.01 sec)
```

修改全局模式：
```
set global sql_mode ='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';
```

重启MySQL服务，再次查看MySQL模式。

我们也可以只修改要改的库，选择表：
> use test;

执行：
```
set sql_mode ='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';
```

查看MySQL模式，发现 `ONLY_FULL_GROUP_BY` 已经不在里面了。 

或者直接设置为 非严格模式：
```
set sql_mode = '';
```

## 拓展

有时 SELECT 查询时会同时用到 GROUP BY 和 ORDER BY，使用不当就会报错，举一个正确的例子，理解一下。

一个年级有多个班，每个班有多个学生，每个学生都有自己考试的成绩，如何读出每个班中成绩分数最大的学生。

正确的写法：`select max(chengji) from record group by ban order by max(chengji);`







<br/><br/><br/><br/><br/>
## 参考资料



