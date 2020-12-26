---
layout: post
categories: MySQL
title: MySQL only_full_group_by错误处理
meta: MySQL only_full_group_by错误处理
---
* content
{:toc}

### 正文

在MySQL的使用中，如select查询和create创建表时，可能会报错：
```
[Err] 1055 - Expression #1 of ORDER BY clause is not in GROUP BY clause 
and contains nonaggregated column 'information_schema.PROFILING.SEQ' 
which is not functionally dependent on columns in GROUP BY clause; 
this is incompatible with sql_mode=only_full_group_by
```

翻译过来是： ORDER BY子句不在GROUP BY子句中，包含非聚合列 `information_schema.PROFILING.SEQ`，
它在功能上不依赖于GROUPBY子句中的列，这与sql模式 `only_full_group_by` 不兼容。

大意是说 GROUP BY 的 字段未在 select 中出现一类的，这是MySQL严格模式，我们修改下模式就好了。

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

再次查看MySQL模式，可能发现没有变化，那我们只修改要改的表，选择表：
> use test;

重新执行：
```
set sql_mode ='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';
```

查看MySQL模式，发现 `ONLY_FULL_GROUP_BY` 已经不在里面了。 

或者直接设置为 非严格模式：
```
set sql_mode = '';
```

<br/><br/><br/><br/><br/>
### 参考资料：



