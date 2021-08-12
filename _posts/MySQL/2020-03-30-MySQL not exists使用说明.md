---
layout: post
categories: MySQL
title: MySQL not exists使用说明
meta: MySQL not exists使用说明
---
* content
{:toc}

### 正文

如果碰到要查询：在一个表中存在，在另一个表中不存在的数据，sql语句应该怎么写？MySQL 提供了EXISTS和NOT EXISTS。

看一下：
```
SELECT c.CustomerId, c.CompanyName  
FROM Customers c  
WHERE EXISTS(  
    SELECT OrderID FROM Orders o  
    WHERE o.CustomerID = c.CustomerID)  
```

EXISTS用于检查子查询是否至少会返回一行数据，该子查询实际上并不返回任何数据，而是返回值True或False。
EXISTS 指定一个子查询，检测行的存在。语法：EXISTS subquery。参数 subquery 是一个受限的 SELECT 语句 （不允许有 COMPUTE 子句和 INTO 关键字）。
结果类型为 Boolean，如果子查询包含行，则返回 TRUE。
SELECT OrderID 中的 OrderID 可以换成 NULL ，或者 * ，或者 1， 等任何值，因为EXISTS的返回值是True或False，与查询字段无关。

哪些种类的商店出现在一个或多个城市里？
```
SELECT DISTINCT store_type FROM stores
WHERE EXISTS (
    SELECT 1 FROM cities_stores
    WHERE cities_stores.store_type = stores.store_type);
```

哪些种类的商店没有出现在任何城市里？
```
SELECT DISTINCT store_type FROM stores
WHERE NOT EXISTS (
    SELECT 1 FROM cities_stores
    WHERE cities_stores.store_type = stores.store_type);
```

哪些种类的商店出现在所有城市里？
```
SELECT DISTINCT store_type FROM stores
WHERE NOT EXISTS (
    SELECT * FROM cities 
    WHERE NOT EXISTS (
        SELECT * FROM cities_stores
        WHERE cities_stores.city = cities.city
        AND cities_stores.store_type = stores.store_type
    )
);
```

<br/><br/><br/><br/><br/>
### 参考资料
   
MySQL文档 13.2.8. Subquery语法 <https://www.mysqlzh.com/doc/126/647.html>

MySQL exists 和 not exists 的用法 <https://www.cnblogs.com/lixiaozhi/p/7965906.html>




