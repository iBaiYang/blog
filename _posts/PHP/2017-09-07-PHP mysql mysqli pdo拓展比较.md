---
layout: post
categories: PHP
title: PHP mysql mysqli pdo拓展比较
meta: mysql mysqli pdo拓展比较
---
* content
{:toc}

### 正文

mysql拓展在php7中被抛弃了，转而支持mysqli升级拓展，所以先写mysqli使用的例子。

```php
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

<br/><br/><br/><br/><br/>
### 参考资料

<http://www.runoob.com/php/php-pdo.html>

<http://www.runoob.com/php/php-ref-mysqli.html>
