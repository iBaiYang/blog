---
layout: post
categories: PHP
title: PHP 操作SQLite
meta: PHP 操作SQLite
---
* content
{:toc}

## 引言

SQLite是一个进程内的库，实现了自给自足的、无服务器的、零配置的、事务性的 SQL 数据库引擎。它是一个零配置的数据库，这意味着与其他数据库不一样，您不需要在系统中配置。

就像其他数据库，SQLite 引擎不是一个独立的进程，可以按应用程序需求进行静态或动态连接。SQLite 直接访问其存储文件。

目前，几乎所有版本的 Linux 操作系统都附带 SQLite。所以，只要使用下面的命令来检查您的机器上是否已经安装了 SQLite。

```bash
sqlite3 --version
```

如果您的系统上没有安装 SQLite，您可以使用以下命令安装它：

```bash
# 在基于 Debian 的系统上（如 Ubuntu）
sudo apt-get install sqlite3

# 在基于 Red Hat 的系统上（如 CentOS）
sudo yum install sqlite
```

SQLite官网 [https://www.sqlite.org](https://www.sqlite.org) 。

命令行操作不太直观，可以下载SQLite Database Browser进行操作 [https://sqlitebrowser.org/dl](https://sqlitebrowser.org/dl)。

## 使用 SQLite3 扩展

### 连接数据库

```php
<?php
// 创建或连接数据库（文件不存在时自动创建）
$db = new SQLite3('./mydatabase.db');

// 检查连接是否成功
if (!$db) {
    die("连接数据库失败");
}

echo "数据库连接成功";
?>
```

### 创建表

```php
// 创建用户表
$query = "CREATE TABLE IF NOT EXISTS users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    email TEXT UNIQUE NOT NULL,
    age INTEGER,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
)";

$result = $db->exec($query);
if ($result) {
    echo "表创建成功";
} else {
    echo "表创建失败: " . $db->lastErrorMsg();
}
```

### 插入数据

```php
// 方法1：直接执行
$db->exec("INSERT INTO users (name, email, age) VALUES ('张三', 'zhangsan@example.com', 25)");

// 方法2：使用预处理语句（推荐，防止SQL注入）
$stmt = $db->prepare("INSERT INTO users (name, email, age) VALUES (:name, :email, :age)");
$stmt->bindValue(':name', '李四', SQLITE3_TEXT);
$stmt->bindValue(':email', 'lisi@example.com', SQLITE3_TEXT);
$stmt->bindValue(':age', 30, SQLITE3_INTEGER);
$stmt->execute();

// 获取最后插入的ID
$lastId = $db->lastInsertRowID();
echo "最后插入的ID: " . $lastId;
```

### 查询数据

```php
// 查询所有数据
$result = $db->query("SELECT * FROM users");
while ($row = $result->fetchArray(SQLITE3_ASSOC)) {
    echo "ID: " . $row['id'] . ", 姓名: " . $row['name'] . ", 邮箱: " . $row['email'] . "\n";
}

// 查询单条数据
$stmt = $db->prepare("SELECT * FROM users WHERE id = :id");
$stmt->bindValue(':id', 1, SQLITE3_INTEGER);
$result = $stmt->execute();
$row = $result->fetchArray(SQLITE3_ASSOC);
if ($row) {
    echo "找到用户: " . $row['name'];
}

// 查询数量
$count = $db->querySingle("SELECT COUNT(*) FROM users");
echo "总用户数: " . $count;
```

### 更新数据

```php
$stmt = $db->prepare("UPDATE users SET age = :age WHERE id = :id");
$stmt->bindValue(':age', 26, SQLITE3_INTEGER);
$stmt->bindValue(':id', 1, SQLITE3_INTEGER);
$result = $stmt->execute();

if ($result) {
    echo "更新成功，影响行数: " . $db->changes();
}
```

### 删除数据

```php
$stmt = $db->prepare("DELETE FROM users WHERE id = :id");
$stmt->bindValue(':id', 2, SQLITE3_INTEGER);
$result = $stmt->execute();

if ($result) {
    echo "删除成功";
}
```

## 使用 PDO 扩展

### 连接数据库

```php
<?php
try {
    $pdo = new PDO('sqlite:./mydatabase.db');
    // 设置错误模式为异常
    $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
    echo "PDO连接成功";
} catch (PDOException $e) {
    die("连接失败: " . $e->getMessage());
}
?>
```

### 基本操作示例

```php
// 创建表
$pdo->exec("CREATE TABLE IF NOT EXISTS products (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    price REAL,
    stock INTEGER DEFAULT 0
)");

// 插入数据
$stmt = $pdo->prepare("INSERT INTO products (name, price, stock) VALUES (?, ?, ?)");
$stmt->execute(['iPhone', 5999.99, 100]);
$stmt->execute(['iPad', 3999.99, 50]);

// 查询数据
$stmt = $pdo->query("SELECT * FROM products");
$products = $stmt->fetchAll(PDO::FETCH_ASSOC);

foreach ($products as $product) {
    echo "产品: {$product['name']}, 价格: {$product['price']}\n";
}

// 事务处理
try {
    $pdo->beginTransaction();
    
    $stmt = $pdo->prepare("UPDATE products SET stock = stock - ? WHERE id = ?");
    $stmt->execute([1, 1]); // 减少库存
    
    // 其他操作...
    
    $pdo->commit();
    echo "事务执行成功";
} catch (Exception $e) {
    $pdo->rollBack();
    echo "事务失败: " . $e->getMessage();
}
```

## 完整的 CRUD 类示例

```php
<?php
class SQLiteManager {
    private $db;
    
    public function __construct($databaseFile) {
        $this->db = new SQLite3($databaseFile);
    }
    
    // 创建表
    public function createTable() {
        $sql = "CREATE TABLE IF NOT EXISTS books (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            title TEXT NOT NULL,
            author TEXT NOT NULL,
            price REAL,
            publish_date DATE
        )";
        
        return $this->db->exec($sql);
    }
    
    // 添加书籍
    public function addBook($title, $author, $price, $publishDate = null) {
        $stmt = $this->db->prepare("
            INSERT INTO books (title, author, price, publish_date) 
            VALUES (:title, :author, :price, :publish_date)
        ");
        
        $stmt->bindValue(':title', $title, SQLITE3_TEXT);
        $stmt->bindValue(':author', $author, SQLITE3_TEXT);
        $stmt->bindValue(':price', $price, SQLITE3_FLOAT);
        $stmt->bindValue(':publish_date', $publishDate, SQLITE3_TEXT);
        
        return $stmt->execute();
    }
    
    // 获取所有书籍
    public function getAllBooks() {
        $result = $this->db->query("SELECT * FROM books ORDER BY id DESC");
        $books = [];
        
        while ($row = $result->fetchArray(SQLITE3_ASSOC)) {
            $books[] = $row;
        }
        
        return $books;
    }
    
    // 根据ID获取书籍
    public function getBookById($id) {
        $stmt = $this->db->prepare("SELECT * FROM books WHERE id = :id");
        $stmt->bindValue(':id', $id, SQLITE3_INTEGER);
        $result = $stmt->execute();
        
        return $result->fetchArray(SQLITE3_ASSOC);
    }
    
    // 更新书籍
    public function updateBook($id, $title, $author, $price, $publishDate) {
        $stmt = $this->db->prepare("
            UPDATE books SET 
            title = :title, 
            author = :author, 
            price = :price, 
            publish_date = :publish_date 
            WHERE id = :id
        ");
        
        $stmt->bindValue(':id', $id, SQLITE3_INTEGER);
        $stmt->bindValue(':title', $title, SQLITE3_TEXT);
        $stmt->bindValue(':author', $author, SQLITE3_TEXT);
        $stmt->bindValue(':price', $price, SQLITE3_FLOAT);
        $stmt->bindValue(':publish_date', $publishDate, SQLITE3_TEXT);
        
        return $stmt->execute();
    }
    
    // 删除书籍
    public function deleteBook($id) {
        $stmt = $this->db->prepare("DELETE FROM books WHERE id = :id");
        $stmt->bindValue(':id', $id, SQLITE3_INTEGER);
        
        return $stmt->execute();
    }
    
    public function __destruct() {
        $this->db->close();
    }
}

// 使用示例
$dbManager = new SQLiteManager('books.db');
$dbManager->createTable();

// 添加书籍
$dbManager->addBook('PHP编程', '张三', 59.99, '2023-01-01');
$dbManager->addBook('SQLite指南', '李四', 39.99, '2023-02-15');

// 获取所有书籍
$books = $dbManager->getAllBooks();
foreach ($books as $book) {
    echo "《{$book['title']}》 - {$book['author']} - ￥{$book['price']}\n";
}
?>
```

## 实用技巧

### 错误处理

```php
try {
    $db = new SQLite3('./test.db');
    
    // 启用异常
    $db->enableExceptions(true);
    
    // 执行查询
    $result = $db->query("SELECT * FROM non_existent_table");
    
} catch (Exception $e) {
    echo "错误: " . $e->getMessage();
}
```

### 备份数据库

```php
function backupSQLite($sourceFile, $backupFile) {
    if (copy($sourceFile, $backupFile)) {
        echo "备份成功: " . $backupFile;
    } else {
        echo "备份失败";
    }
}

backupSQLite('./mydatabase.db', './backup_' . date('Y-m-d') . '.db');
```

### 数据库信息查询

```php
// 获取所有表名
$result = $db->query("SELECT name FROM sqlite_master WHERE type='table'");
while ($row = $result->fetchArray()) {
    echo "表名: " . $row['name'] . "\n";
}

// 获取表结构
$result = $db->query("PRAGMA table_info(users)");
while ($row = $result->fetchArray(SQLITE3_ASSOC)) {
    echo "字段: {$row['name']} 类型: {$row['type']}\n";
}
```

### 事务处理

```php
try {
    // 开启事务
    $db->exec('BEGIN TRANSACTION');
    
    // 执行多个操作
    $db->exec("UPDATE accounts SET balance = balance - 100 WHERE id=1");
    $db->exec("UPDATE accounts SET balance = balance + 100 WHERE id=2");
    
    // 提交事务
    $db->exec('COMMIT');
} catch (Exception $e) {
    $db->exec('ROLLBACK');
    die("事务失败: " . $e->getMessage());
}
```

## 总结

1. **SQLite3扩展**：轻量级，面向对象接口
2. **PDO扩展**：更通用，支持多种数据库，更好的错误处理
3. **始终使用预处理语句**防止SQL注入
4. **合理处理错误和异常**
5. **及时关闭数据库连接**

根据项目需求选择合适的扩展，小型项目推荐使用 SQLite3，大型或需要数据库迁移的项目推荐使用 PDO。



## 参考资料





