---
layout: post
categories: MySQL
title: MySQL Index column size too large. The maximum column size is 767 bytes
meta: MySQL Index column size too large. The maximum column size is 767 bytes
---
* content
{:toc}

## 正文

今天新建一个表时报错了：

```
SQL 错误 [1709] [HY000]: Index column size too large. The maximum column size is 767 bytes.
```

如：

```
mysql> use MyDB;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> CREATE TABLE `TEST` (
    ->   `CODE_NAME` varchar(100) NOT NULL DEFAULT '',
    ->   `CODE_SEQ` smallint(6) NOT NULL DEFAULT '1',
    ->   `ACTIVE` char(1) DEFAULT 'Y',
    ->   `CODE_VALUE1` varchar(250) DEFAULT NULL,
    ->   PRIMARY KEY (`CODE_NAME`,`CODE_SEQ`),
    ->   KEY `IDX_GEN_CODE` (`CODE_NAME`,`CODE_VALUE1`)
    -> ) ENGINE=InnoDB DEFAULT CHARSET=utf8;
Query OK, 0 rows affected (0.02 sec)

mysql> ALTER TABLE TEST MODIFY CODE_VALUE1 VARCHAR(350);
ERROR 1071 (42000): Specified key was too long; max key length is 767 bytes
mysql> 
```

错误是指超出索引字节的限制。

### 原因

在MySQL 5.5 版本中引入了系统变量innodb_large_prefix，用来禁用大型前缀索引，以便与不支持大索引键前缀的早期版本的InnoDB兼容。
innodb_large_prefix 在MySQL版本为  5.6.41 默认是关闭的，在 5.7 默认是开启的。
innodb_large_prefix将在以后的版本中删除、弃用。

如果启用了系统变量innodb_large_prefix，则对于使用DYNAMIC或COMPRESSED行格式的InnoDB表，索引键前缀限制为3072字节。
如果禁用innodb_large_prefix，则对于任何行格式的表，索引键前缀限制为767字节。
例如，您可能会在TEXT或VARCHAR列上使用超过255个字符的列前缀索引达到此限制，假设为utf8mb3字符集，并且每个字符最多包含3个字节。

适用于索引键前缀的限制也适用于全列索引键。

注意：上面是767个字节，而不是字符，具体到字符数量，这就跟字符集有关。GBK是双字节的，UTF-8是三字节的。

### 解决方案

有两种方案：启用系统变量innodb_large_prefix、使用前缀索引解决这个问题。

#### 启用系统变量innodb_large_prefix

启用系统变量innodb_large_prefix

注意：光有这个系统变量开启是不够的。必须满足下面几个条件：
1. 系统变量innodb_large_prefix为ON
2. 系统变量innodb_file_format为Barracuda
3. ROW_FORMAT为DYNAMIC或COMPRESSED

如下测试所示：
```
mysql> show variables like '%innodb_large_prefix%';
+---------------------+-------+
| Variable_name       | Value |
+---------------------+-------+
| innodb_large_prefix | OFF   |
+---------------------+-------+
1 row in set (0.00 sec)

mysql> set global innodb_large_prefix=on;
Query OK, 0 rows affected (0.00 sec)

mysql> ALTER TABLE TEST MODIFY CODE_VALUE1 VARCHAR(350);
ERROR 1709 (HY000): Index column size too large. The maximum column size is 767 bytes.
mysql> 
mysql> show variables like '%innodb_file_format%';
+--------------------------+-----------+
| Variable_name            | Value     |
+--------------------------+-----------+
| innodb_file_format       | Antelope  |
| innodb_file_format_check | ON        |
| innodb_file_format_max   | Barracuda |
+--------------------------+-----------+
3 rows in set (0.01 sec)

mysql> set global innodb_file_format=Barracuda;
Query OK, 0 rows affected (0.00 sec)

mysql> ALTER TABLE TEST MODIFY CODE_VALUE1 VARCHAR(350);
ERROR 1709 (HY000): Index column size too large. The maximum column size is 767 bytes.
mysql> 

mysql> 
mysql> show table status from MyDB where name='TEST'\G;
*************************** 1. row ***************************
           Name: TEST
         Engine: InnoDB
        Version: 10
     Row_format: Compact
           Rows: 0
 Avg_row_length: 0
    Data_length: 16384
Max_data_length: 0
   Index_length: 16384
      Data_free: 0
 Auto_increment: NULL
    Create_time: 2018-09-20 13:53:49
    Update_time: NULL
     Check_time: NULL
      Collation: utf8_general_ci
       Checksum: NULL
 Create_options: 
        Comment: 

mysql>  ALTER TABLE TEST ROW_FORMAT=DYNAMIC;
Query OK, 0 rows affected (0.05 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> show table status from MyDB where name='TEST'\G;
*************************** 1. row ***************************
           Name: TEST
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 0
 Avg_row_length: 0
    Data_length: 16384
Max_data_length: 0
   Index_length: 16384
      Data_free: 0
 Auto_increment: NULL
    Create_time: 2018-09-20 14:04:05
    Update_time: NULL
     Check_time: NULL
      Collation: utf8_general_ci
       Checksum: NULL
 Create_options: row_format=DYNAMIC
        Comment: 
1 row in set (0.00 sec)

ERROR: 
No query specified

mysql> ALTER TABLE TEST MODIFY CODE_VALUE1 VARCHAR(350);
Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

#### 使用前缀索引解决这个问题

之所以要限制索引键值的大小，是因为性能问题，而前缀索引能很好的解决这个问题。不需要修改任何系统变量。

```
mysql> show index from TEST;
..................................

mysql> ALTER TABLE TEST DROP INDEX IDX_GEN_CODE;
Query OK, 0 rows affected (0.00 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> CREATE IDX_GEN_CODE TEST ON TEST (CODE_NAME, CODE_VALUE1(12));
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE TEST MODIFY CODE_VALUE1 VARCHAR(350);
Query OK, 1064 rows affected (0.08 sec)
Records: 1064  Duplicates: 0  Warnings: 0
```




<br/><br/><br/><br/><br/>
## 参考资料
   
ERROR 1071 (42000): Specified key was too long; max key length is 767 bytes <https://www.cnblogs.com/kerrycode/p/9680881.html>



