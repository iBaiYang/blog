---
layout: post
categories: Yii2
title: Yii2 Elasticsearch查询封装实现
meta: Elasticsearch查询封装实现
---
* content
{:toc}

### 正文

PHP查询Elasticsearch数据，可以直接用Packagist中的类库elasticsearch/elasticsearch。具体安装方法，看一下过去的记录。

查询条件一般用法：
```
$condition = [
    "index" => "weberror-*",
    "type" => "error",
    "body" => [
        "_source" => [
            "includes" => ["user_id", "name", age"]
        ],
        "from" => 0,
        "size" => 100,
        "query" => [
            "bool"=>[
                "filter"=> [
                    ["match_phrase" => ["name" => "sam li"]],
                    ["bool" => [
                        "must_not" => ["term" => ["name" => "harry"]]
                    ]],
                    ["bool" => [
                        "filter" => [
                            ["match_phrase" => ["name" => "potter wang"]],
                            ["bool" => [
                                "must_not" => ["term" => ["name" => "moon"]]
                            ]]
                        ]
                    ]],
                ],
                "should" => [
                    ["term" => ["name" => "sam"]],
                    ["range" => [
                        "age" => ["gte" => 18]
                    ]],
                    ["range" => [
                        "age" => ["lt" => 60]
                    ]],
                ]
            ]
        ],
        "sort" => [
            "age" => [
                "order"=>"desc"
            ],
            "id" => [
                "order"=>"asc"
            ]
        ]
    ]
];

$client = ClientBuilder::create()->setHosts(["http://127.0.0.1:9200","http://127.0.0.2:9200"])->build();

$response = $client->search($condition);
```

如果Elasticsearch查询不进行封装，代码看起来会很散，我们在Yii2中封装一下Elasticsearch查询，其实就是把最终输出的查询条件如上所示一样。

能不能改成下面Yii2 model使用的这种查询方式呢？
````
UserInfo::find()
    ->select("user_id, user_name, age")
    ->where(["=" => ["userId" => 101]])
    ->addWhere(["like" => ["userName" => "zhang san"]])
    ->addWhere([
            "add" => [
                "<>" => ["userId" => 0,
                "like" => ["userName" => "zhang"],
            ]
        ])
    ->setOrder(["age" => "desc"])
    ->setOffset(0)
    ->setLimit(10)
    ->search();
````

Es连接读取层 与 AR活动记录 首先要进行分离， AR活动记录 通过 ActiveQuery活动搜索层 对 Es连接读取层 进行数据读取。

#### Es连接读取层

```php
<?php
namespace common\components;

use Elasticsearch\Client;
use Elasticsearch\ClientBuilder;
use yii\base\Component;

class LElasticSearch extends Component
{
    public $host;
    /** @var int  */
    public $retries;
    // HTTP handler
    public $handler;
    /** @var  Client $instance */
    private $instance;

    public $timeout = 3;
    public $connectTimeout = 3;
    
    const arOperator = [
        ">",
        ">=",
        "<",
        "<=",
        "or",
        "and",
        "like",
        "in",
        "=",
        "not in",
        "<>"
    ];
    
    public function init()
    {
        if (!$this->handler) {
            $this->handler = ClientBuilder::singleHandler();
        }
        $this->instance = ClientBuilder::create()->setHandler($this->handler)->setHosts($this->host)->setRetries($this->retries)->build();
    }

    public function getInstance()
    {
        return $this->instance;
    }
    
    public function search($param, $needBuild = true)
    {
        if ($needBuild) {
            $param["body"] = $this->buildParam($param["body"]);
        }
        
        if (empty($param["client"]["timeout"])) {
            $param["client"]["timeout"] = $this->timeout;
        }
        
        if (empty($param["client"]["connect_timeout"])) {
            $param["client"]["connect_timeout"] = $this->connectTimeout;
        }

        $data = $this->instance->search($param);
        $returnData = [
            "count" => $data["hits"]["total"],
            "list" => [],
        ];
        
        if ($data["hits"]["hits"]){
            $tmpData = [];
            foreach ($data["hits"]["hits"] as $value)
            {
                $tmpData[] = $value["_source"];
            }
            $returnData["list"] = $tmpData;
        }
        
        return $returnData;
    }
    
    /**
     * 数据结构，只支持简单查询，不支持group by
     * @param $param 
     * e.g:
     * {
     *    "select": ["sd","sd"],
     *    "where": {
     *      "and": [
     *          "in":["key":[1,3,4]],
     *          ">":["key":123],
     *          "or":[
     *              ["like": ["key":"value"]],
     *              ["=", ["key":"value"]]
     *          ],
     *          "like": ["key"=>"value"],
     *      ],
     *      "or":[
     *          ["like": ["key":"value"]],
     *          ["=", ["key":"value"]]
     *      ]
     *   },
     *   "order": [["dadf":"desc"],["asdf":"asc"]],
     *   "limit":5,
     *   "offset":6
     * }
     * 
     * @return mixed
     */
    public function buildParam($param)
    {
        if (!is_array($param)) {
            return $param;
        }
        
        $returnParam = [];
        $condition = [];
//        if (!empty($param["groupBy"]))
//        {
//            if (is_array($param["groupBy"]))
//            {
//                $newTmp = [];
//                for ($i = count($param["groupBy"]); $i > 0; $i--)
//                {
//                    $group = $param["groupBy"][$i-1];
//                    $tmp["aggs"] = [
//                        $group => [
//                            "terms" => [
//                                  "field"=>$group,
//                              ]
//                        ]
//                    ];
//                    if ($newTmp == [])
//                    {
//                        $newTmp = $tmp;
//                    } else {
//                        $tmp["aggs"][$group] = $newTmp;
//                        $newTmp = $tmp;
//                    }
//                }
//            }
//            $returnParam = $newTmp;
//        }
        if (!empty($param["select"])) {
            $select = $param["select"];
            if (is_string($select)) {
                if (strpos($select, ",") !== false) {
                    $select = explode(",", $select);
                }
            }
            $returnParam["_source"] = [
                "includes" => $select,
            ];
        }
        if (!empty($param["limit"])) {
            $returnParam["size"] = $param["limit"];
        }
        if (!empty($param["offset"])) {
            $returnParam["from"] = $param["offset"];
        }
        if (!empty($param["order"])) {
            foreach ($param["order"] as $value)
            {
                foreach ($value as $k => $v)
                {
                    $returnParam["sort"][$k] = ["order" => $v];
                }
            }

        }

        if (!empty($param["where"])) {
            foreach ($param["where"] as $key => $value) {
                if ($key == "and")
                { //这里使用filter，主要是为了过滤，没必要使用must
                    if (is_array($value))
                    {
                        foreach ($value as $k => $v)
                        {
                            foreach ($v as $k1 => $v1)
                            {
                                if ($k1 == "and" || $k1 == "or")
                                {
                                    $esCondition = $this->buildParam(["where" => [$k1 => $v1]]);
                                    $condition["bool"]["filter"][] = $esCondition["query"];
                                } else {
                                    $esCondition = $this->parse([$k1 => $v1]);
                                    if ($esCondition)
                                    {
                                        $condition["bool"]["filter"][] = $esCondition;
                                    }
                                }
                            }
                        }
                    }
  
                } else if ($key == "or") {
                    if (is_array($value))
                    {
                        foreach ($value as $k => $v)
                        {
                            foreach ($v as $k1 => $v1)
                            {
                                if ($k1 == "and" || $k1 == "or")
                                {
                                    $esCondition = $this->buildParam(["where" => [$k1 => $v1]]);
                                    $condition["bool"]["should"][] = $esCondition["query"];
                                } else {
                                    $esCondition = $this->parse([$k1 => $v1]);
                                    if ($esCondition)
                                    {
                                        $condition["bool"]["should"][] = $esCondition;
                                    }
                                }
                            }
                        }
                    }
                }
            }
        }
        
        if ($condition) {
            $returnParam["query"] = $condition;
        } else {
            $returnParam["query"] = new \stdClass();
        }

        return $returnParam;
    }

    private function parse($param)
    {
        if (!is_array($param) || empty($param)) {
            return false;
        }
        
        $condition = [];
        foreach ($param as $key => $value)
        {
            switch ($key) 
            {
                case "in":
                    $condition = [
                        "terms" => $value
                    ];
                    break;
                case "=":
                    $condition = [
                        "term" => $value
                    ];
                    break;
                case ">=":
                    $k = array_keys($value);
                    $v = array_values($value);
                    $condition = [
                        "range" => [
                            $k[0] => [
                                "gte" => $v[0]
                            ]
                        ]
                    ];
                    break;
                case ">":
                    $k = array_keys($value);
                    $v = array_values($value);
                    $condition = [
                        "range" => [
                            $k[0] => [
                                "gt" => $v[0]
                            ]
                        ]
                    ];
                    break;
                case "<":
                    $k = array_keys($value);
                    $v = array_values($value);
                    $condition = [
                        "range" => [
                            $k[0] => [
                                "lt" => $v[0]
                            ]
                        ]
                    ];
                    break;
                case "<=":
                    $k = array_keys($value);
                    $v = array_values($value);
                    $condition = [
                        "range" => [
                            $k[0] => [
                                "lte" => $v[0]
                            ]
                        ]
                    ];
                    break;
                case "like":
                    $condition = [
                        "match_phrase" => $value
                    ];
                    break;
                case "not in":
                    $condition = [
                        "bool" => [
                            "must_not" => [
                                "terms" => $value
                            ]
                        ]
                    ];
                    break;
                case "<>":
                    $condition = [
                        "bool" => [
                            "must_not" => [
                                "term" => $value
                            ]
                        ]
                    ];
                    break;
                case "_script":
                    $condition = [
                        "script" => [
                            "script" => $value,
                        ]
                    ];
                    break;
                default:
                    $condition = false;
            }
        }
        
        return $condition;
    }
    
    /**
     * 是否存在操作符为key
     * @param $param
     * @return bool
     */
    public function isExistOperator($param)
    {
        foreach ($param as $key => $value) 
        {
            if (in_array($key, LElasticSearch::arOperator)) {
                return true;
            }
        }
        
        return false;
    }
}
```

上面是LElasticSearch组件，写好config配置，Yii2项目加载时会自动初始化：
```
return [
    ...
    "components" => [
        ...
        "es" => [
            "class" => "common\components\LElasticSearch",
            "host" => [
                "http://127.0.0.1:9200",
                "http://127.0.0.2:9200",
            ],
        ],
    ],
    "params" => [
        ...
    ],
    ...
];
```

#### AR活动记录

```php
<?php
namespace common\components\ES;

/**
 * interface类，由model类实现
 * Interface ActiveRecordInterface
 * @package common\components\ES
 */
interface ActiveRecordInterface {
    /**
     * 获取es的链接
     * @return mixed
     */
    public static function getES();

    /**
     * 获取index
     * @return mixed
     */
    public static function getIndex();

    /**
     * 获取type
     * @return mixed
     */
    public static function getType();

    /**
     * key的对应关系
     * @return mixed
     */
    public function getMapping();
}
```

```php
<?php
namespace common\components\ES;

use Yii;

abstract class ActiveRecord extends \ArrayObject implements ActiveRecordInterface
{
    /**
     * Populates an active record object using a row of data from the database/storage.
     *
     * This is an internal method meant to be called to create active record objects after
     * fetching data from the database. It is mainly used by [[ActiveQuery]] to populate
     * the query results into active records.
     *
     * When calling this method manually you should call [[afterFind()]] on the created
     * record to trigger the [[EVENT_AFTER_FIND|afterFind Event]].
     *
     * @param BaseActiveRecord $record the record to be populated. In most cases this will be an instance
     * created by [[instantiate()]] beforehand.
     * @param array $row attribute values (name => value)
     */
    public static function populateRecord($record, $row)
    {
        foreach ($row as $name => $value) {
            $propertyName = $record->getProperty($record, $name);
            if ($propertyName != null) {
                $record->$propertyName = $value;
            }
        }
    }
    
    public function getProperty($record, $name)
    {
        $mapping = $record->getMapping();
        return isset($mapping[$name]) ? $mapping[$name] : $name;
    }
    
    /**
     * @inheritdoc
     * @return ActiveQuery the newly created [[ActiveQuery]] instance.
     */
    public static function find()
    {
        return Yii::createObject(ActiveQuery::className(), [get_called_class()]);
    }
}
```

#### ActiveQuery活动搜索层

```php
<?php
namespace common\components\ES;

use common\components\LElasticSearch;
use Yii;
use yii\base\Object;

class ActiveQuery extends Object
{
    private $where = [];
    private $sort = [];
    private $select = [];
    private $limit = 10;
    private $offset = 0;
    private $modelClass;
    private $isArray = true;

    public function asArray()
    {
        $this->isArray = true;
    }

    public function __construct($modelClass, $config = [])
    {
        $this->modelClass = $modelClass;
        parent::__construct($config);
    }
    
    public function addWhere($where)
    {
        $this->where[] = $where;
        return $this;
    }
    public function where($where)
    {
        $this->where = $where;
        return $this;
    }

    public function select($select) {
        $this->select = $select;
        return $this;
    }

    public function addOrderBy($order) {
        $this->sort[] = $order;
        return $this;
    }

    public function setOrder($order)
    {
        $this->sort = $order;
        return $this;
    }

    public function setOffset($offset)
    {
        $this->offset = $offset;
        return $this;
    }

    public function setLimit($limit)
    {
        $this->limit = $limit;
        return $this;
    }
    
    /**
     * @param LElasticSearch $es
     * @param bool $needBuild
     * @return array
     */
    public function search($es = null, $needBuild = true)
    {
        $modelClass = $this->modelClass;
        $index = $modelClass::getIndex();
        $type = $modelClass::getType();
        $param = [
            "index" => $index,
            "type"  => $type,
            "body"  => $this->getBody(),
        ];
        if ($es == null) {
            $es = Yii::$app->es;
        }
        $searchData = $es->search($param, $needBuild);
        if (count($searchData["list"]) == 0) {
            return $searchData;
        } else if (!$this->isArray) {
            // 返回对象
            $tmp = [];
            foreach ($searchData["list"] as $value)
            {
                $tmp[] = $this->getActiveRecordModel($value);
            }
            $searchData["list"] = $tmp;
        }
        
        return $searchData;
    }
    
    /**
     * 返回es的body数组，按照LElasticSearch来
     */
    private function getBody()
    {
        $returnBody = [];
        
        if ($this->where) {
            $returnBody["where"] = ["and" => $this->where];
        }
        if ($this->select) {
            $returnBody["select"] = $this->select;
        }
        if ($this->sort) {
            $returnBody["order"] = $this->sort;
        }
        if ($this->limit) {
            $returnBody["limit"] = $this->limit;
        }
        if ($this->offset) {
            $returnBody["offset"] = $this->offset;
        }
        
        return $returnBody;
    }
    
    /**
     * 返回ActiveRecord对象
     * @param $data
     * @return ActiveRecord
     */
    private function getActiveRecordModel($data)
    {
        $modelClass = $this->modelClass;
        /** @var ActiveRecord $class */
        $class = new \ReflectionClass($modelClass);
        $instance = $class->newInstance();
        $modelClass::populateRecord($instance, $data);
        
        return $instance;
    }
    
    public function one($es = null)
    {
        $this->limit=1;
        $this->offset = 0;
        
        return $this->search($es);
    }
}
```

#### Model类

看个例子：
```php
<?php
namespace common\esModels;

use common\components\ES\ActiveRecord;
use Yii;

/**
 * 用户类model
 * Class UserInfo
 * @package common\esModels
 */
class UserInfo extends ActiveRecord 
{
    /**
     * 获取es的链接
     * @return mixed
     */
    public static function getES()
    {
        return Yii::$app->es;
    }

    /**
     * @return mixed
     */
    public static function getIndex()
    {
        return "user_info";
    }

    /**
     * @return mixed
     */
    public static function getType()
    {
        return "user_info";
    }

    public function getMapping()
    {
        return [
            "userId" => "user_id",
            "userName" => "user_name",
            "age" => "age",
        ];
    }
}
```

使用的时候：
```php
<?php
namespace backend\controllers;

use common\esModels\UserInfo;
use Yii;
use yii\web\Controller;

class UserController extends Controller
{
    public function actionGetUserInfoByUserId()
    {
        $user_id = 100;
        
        if ($user_id) {
            $condition = [
                ["=" => ["userId" => $user_id]]
            ];
        
            return UserInfo::find()
                ->select("user_id, user_name, age")
                ->where($condition)
                ->addWhere(["like" => ["userName" => "zhang san"]])
                ->addWhere([
                        "add" => [
                            "<>" => ["userId" => 0,
                            "like" => ["userName" => "zhang"],
                        ]
                    ])
                ->setOrder(["age" => "desc"])
                ->setOffset(0)
                ->setLimit(10)
                ->search();
        }
        
        return [];
    }
}
```

### 释疑点

#### new stdClass()

`new stdClass()`是PHP应用程序的一个变量。PHP可以用 `$object = new StdClass();` 创建一个没有成员方法和属性的空对象。

很多时候，程序员们会将一些参数配置项之类的信息放在数组中使用，但是数组操作起来并不是很方便，很多时候使用`对象操作符->xxx`比数组操作符`['xxx']`要方便不少。
于是就需要创建一个空的对象，来将需要的属性名和属性值存储到对象中。

然而PHP中没有Javascript里面 `var object = {};` 这样的语法。这是StdClass()出现的一个原因。

stdClass在PHP5才开始被流行。而stdClass也是zend的一个保留类。stdClass类是PHP的一个内部保留类，初始时没有成员变量也没成员方法，所有的魔术方法都被设置为NULL。
凡是用new stdClass()的变量，都不可能会出现$a->test()这种方式的使用。PHP5的对象的独特性，对象在任何地方被调用，都是引用地址型的，所以相对消耗的资源会少一点。
在其它页面为它赋值时是直接修改，而不是引用一个拷贝。
它可以用于手动实例化泛型对象，然后可以为这些对象设置成员变量，这对于将对象传递给其他希望以对象作为参数的函数或方法非常有用。
更可能的用法是将数组强制转换为一个对象，该对象接受数组中的每个值，并将其作为成员变量添加，其名称基于数组中的键。

PHP创建空对象可以使用下面这几种方法实现：

**方法一：写一个空类**

```php
<?php
    class cfg {}        
    
    $cfg = new cfg;
    $cfg->dbhost = 'www.123.com';
    echo $cfg->dbhost;
?>
```

勉强能完成任务，但是特别没有格局。

**方法二：实例化 StdClass 类**

```php
<?php
    $cfg = new StdClass();
    $cfg->dbhost = 'www.123.com';
    echo $cfg->dbhost;
?>
```

StdClass类是PHP中的一个基类。StdClass类没有任何成员方法，也没有任何成员属性，实例化以后就是一个空对象。

数组转为对象可以这么用：
```php
<?php
    function arrayToObject($array) {
        if(!is_array($array)) {
            return $array; 
        }
        $object = new stdClass();
        if (is_array($array) && count($array) > 0) {
            foreach ($array as $name => $value) {
                $name = strtolower(trim($name));
                if (!empty($name)) {
                    $object->$name = $value;
                }
            }
            return $object;
        }
        return FALSE; 
    }
?>
```

数组转为对象，也可以使用下面的方法。

**方法三：使用json_encode()和json_decode()**

这种方法就是把一个空的JSON对象通过json_decode()转变为PHP的StdClass空对象。
同样的道理，你可以将一个数组通过json_encode()转成JSON，再通过json_decode()将JSON转为StdClass对象。

```php
<?php
    $cfg = json_decode('{}');
    $cfg->dbhost = 'www.123.com';
    echo $cfg->dbhost;
?>
```

<br/><br/><br/><br/><br/>
### 参考资料

elastic/elasticsearch-php <https://github.com/elastic/elasticsearch-php>

Packagist elasticsearch/elasticsearch <https://packagist.org/packages/elasticsearch/elasticsearch>

PHP Elasticsearch查询服务示例 <https://ibaiyang.github.io/blog/php/2018/12/27/PHP-Elasticsearch%E6%9F%A5%E8%AF%A2%E6%9C%8D%E5%8A%A1%E7%A4%BA%E4%BE%8B.html>

linux 安装Elasticsearch <https://ibaiyang.github.io/blog/linux/2020/04/01/linux-%E5%AE%89%E8%A3%85Elasticsearch.html>

PHP new StdClass() 创建空对象 <https://www.51-n.com/t-4421-1-1.html>
