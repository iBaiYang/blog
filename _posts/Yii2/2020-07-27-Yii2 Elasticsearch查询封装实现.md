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

如果Elasticsearch查询不进行封装，代码看起来会很散，我们在Yii2中封装一下Elasticsearch查询。

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
        '<>'
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
     * e.g:
     * {
     * "select": ["sd","sd"],
     *  "where": {
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
     * },
     * "order": [["dadf":"desc"],['asdf':"asc"]],
     * "limit":5,
     * "offset":6
     * }
     * @param $param
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
                            'script' => $value,
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
class a
{
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
                ->where($condition)
                ->setLimit(10)
                ->search();
        }
        
        return [];
    }
}
```

<br/><br/><br/><br/><br/>
### 参考资料

elastic/elasticsearch-php <https://github.com/elastic/elasticsearch-php>

Packagist elasticsearch/elasticsearch <https://packagist.org/packages/elasticsearch/elasticsearch>

PHP Elasticsearch查询服务示例 <https://ibaiyang.github.io/blog/php/2018/12/27/PHP-Elasticsearch%E6%9F%A5%E8%AF%A2%E6%9C%8D%E5%8A%A1%E7%A4%BA%E4%BE%8B.html>

linux 安装Elasticsearch <https://ibaiyang.github.io/blog/linux/2020/04/01/linux-%E5%AE%89%E8%A3%85Elasticsearch.html>


