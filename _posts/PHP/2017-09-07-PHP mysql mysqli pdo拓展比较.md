---
layout: post
categories: PHP
title: PHP mysql mysqli pdo拓展比较
meta: mysql mysqli pdo拓展比较
---
* content
{:toc}

## 正文

mysqli,mysqlnd,pdo到底是什么，先看下PHP官方的解释：

    MYSQL: This extension is deprecated as of PHP 5.5.0, and has been removed as of PHP 7.0.0. 
    MYSQLI: MySQL Improved Extension 
    MySQLND: MySQL Native Drive 
    PDO: The PHP Data Objects。extension defines a lightweight, consistent interface for accessing databases in PHP。

用中文说:

    MYSQL 也叫 Original MySQL，PHP4版本的MYSQL扩展，从PHP5起已经被废弃，并别从PHP7开始已经被移除。
    MYSQLI 叫做 “MySQL增强扩展”。
    MYSQLND，MYSQL NATIVE DIRVER 叫做MYSQL “官方驱动”或者更加直接点的叫做“原生驱动”
    PDO，PHP Data Objects PHP数据对象，是PHP应用中的一个数据库抽象层规范。
    
在PHP拓展的角度上看，MYSQL 和 MYSQLi 还是比较上层的拓展，依赖更底层的库去连接和访问数据库。
上文所说的 MYSQLND 就是所说的底层的数据库驱动。当然，还有一个驱动叫做libmysqlclient。

总的来说：从应用的层面上看，我们通过PHP 的MYSQL或者MYSQLi扩展提供的API去操作数据库。
从底层来看，MYSQLND提供了底层和数据库交互的支持(可以简单理解为和MySQL server进行网络协议交互)。
而PDO，则提供了一个统一的API接口，使得你的PHP应用不去关心具体要连接的数据库服务器系统类型。
也就是说，如果你使用PDO的API，可以在任何需要的时候无缝切换数据库服务器。比如MYSQL,SQLITE任何数据库都行。
即从大部分功能上看，PDO提供的API接口和MYSQLI提供的接口对于普通的增删改查效果是一致的。

mysql、mysqli是函数式数据库调用，pdo是对象式数据库调用。

mysql拓展在php7中被抛弃了，转而支持mysqli升级拓展，所以先写mysqli使用的例子。

要考虑到SQL注入攻击的[预防处理](https://ibaiyang.github.io/blog/php/2019/03/18/PHP-防SQL注入技术.html) 。

### mysqli

```php
<?php
// mysqli封装类
class MysqliClass
{
     private $DB_HOST = 'localhost';
     private $DB_NAME = 'data_db';
     private $DB_USER = 'root';
     private $DB_PWD = '123456';
     private $DB_PORT = 3306;
     protected $con = '';

     // 构造函数，实例化mysqli连接
     function __construct()
     {
          $this->con = mysqli_connect( $this->DB_HOST, $this->DB_USER, $this->DB_PWD, $this->DB_NAME, $this->DB_PORT );
          // 检查连接
          if ( !$this->con )
          {
               die( "连接错误: " . mysqli_connect_error() );
          }
     }

     // 获取一行数据
     public function find( $sql )
     {
          $result = mysqli_query( $this->con, $sql );
          if ( $result ) {
               $re = mysqli_fetch_array($result, MYSQLI_ASSOC);
               mysqli_free_result($result);

              return $re;
          }
     }

     // 获取多行数据
     public function select( $sql )
     {
          $result = mysqli_query( $this->con, $sql );
          if ( $result ) {
               $re = array();
               while ( $row = mysqli_fetch_assoc( $result ) )
               {
                    $re[] = $row;
               }

          return $re;
          }
     }

     // 获取一个字段的数据
     public function field( $sql )
     {
          $result = mysqli_query( $this->con, $sql );
          if ( $result ) {
               $row = mysqli_fetch_row($result);
               mysqli_free_result($result);

               if ( isset($row[0]) ) {
                    return $row[0];
               }
          }
     }

     // 执行命令  
     public function query( $sql )
     {
          if ( mysqli_query( $this->con, $sql ) ) {
               return mysqli_affected_rows($this->con);
          }
     }
} 
```

### pdo

下面是pdo使用例子:

```php
// pdo封装类
class PdoClass
{
     private $DB_DBMS = 'mysql';
     private $DB_HOST = 'localhost';
     private $DB_NAME = 'data_db';
     private $DB_USER = 'root';
     private $DB_PWD = '123456';
     private $DB_PORT = 3306;
     protected $con = '';


     // 构造函数，实例化pdo连接
     function __construct()
     {
          $dsn = $this->DB_DBMS.':host='.$this->DB_HOST.';dbname='.$this->DB_NAME;
          try {
               $this->con = new PDO( $dsn, $this->DB_USER, $this->DB_PWD ); // 初始化一个PDO对象
               // 默认不是长连接，如果需要数据库长连接，如下
               // $this->con = new PDO( $dsn, $this->DB_USER, $this->DB_PWD, array(PDO::ATTR_PERSISTENT => true) );
          } catch ( PDOException $e ) {
               die ( "Connection failed: " . $e->getMessage() . "
" );
          }
     }


     // 获取一行数据
     public function find( $sql )
     {
          $sth = $this->con->prepare( $sql );
          $sth->execute();
          $result = $sth->fetch(PDO::FETCH_ASSOC);
          // $result = $sth->fetchAll(PDO::FETCH_ASSOC); $result = $result[0]; // 或者用这种方法

          return $result;
     }


     // 获取多行数据
     public function select( $sql )
     {
          $sth = $this->con->prepare( $sql );
          $sth->execute();

          return $sth->fetchAll(PDO::FETCH_ASSOC);
     }


     // 获取一个字段的数据
     public function field( $sql )
     {
          $sth = $this->con->prepare( $sql );
          $sth->execute();
          $result = $sth->fetchColumn();
          // $result = $sth->fetchColumn(1);   // 从结果集中获取第二列

          return $result;
     }


     // 执行命令
     public function query( $sql )
     {
          $sth = $this->con;
          $count = $sth->exec( $sql );

          return $count;
     }
}
```

### mysqlnd



<br/><br/><br/><br/><br/>
## 参考资料

<http://www.runoob.com/php/php-pdo.html>

<http://www.runoob.com/php/php-ref-mysqli.html>

简要谈谈php中mysql,mysqli,mysqlnd,pdo到底是什么 <https://blog.csdn.net/m1ssyAn/article/details/104790740/>

