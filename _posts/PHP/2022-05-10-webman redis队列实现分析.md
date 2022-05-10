---
layout: post
categories: PHP
title: webman redis队列实现分析
meta: webman redis队列实现分析
---
* content
{:toc}

## 正文

webman中redis队列实现用的包是 `webman/redis-queue` ，版本是 `v1.1.0` 。

redis配置文件自动生成在 `config/plugin/webman/redis-queue/redis.php`，内容类似如下：
```php
<?php
return [
    'default' => [
        'host' => 'redis://127.0.0.1:6379',
        'options' => [
            'auth' => '',         // 密码，可选参数
            'db' => 0,            // 数据库
            'max_attempts'  => 5, // 消费失败后，重试次数
            'retry_seconds' => 5, // 重试间隔，单位秒
        ]
    ],
];
```

消费进程配置文件在 `config/plugin/webman/redis-queue/process.php`：
```php
<?php
return [
    'consumer'  => [
        'handler'     => Webman\RedisQueue\Process\Consumer::class,
        'count'       => 6, // 可以设置多进程同时消费
        'constructor' => [
            // 消费者类目录
            'consumer_dir' => app_path() . '/queue/redis'
        ]
    ]
];
```

`Webman\RedisQueue\Process\Consumer` 类文件内容：
```php
<?php
/**
 * This file is part of webman.
 *
 * Licensed under The MIT License
 * For full copyright and license information, please see the MIT-LICENSE.txt
 * Redistributions of files must retain the above copyright notice.
 *
 * @author    walkor<walkor@workerman.net>
 * @copyright walkor<walkor@workerman.net>
 * @link      http://www.workerman.net/
 * @license   http://www.opensource.org/licenses/mit-license.php MIT License
 */

namespace Webman\RedisQueue\Process;

use support\Container;
use Webman\RedisQueue\Client;

/**
 * Class Consumer
 * @package process
 */
class Consumer
{
    /**
     * @var string
     */
    protected $_consumerDir = '';

    /**
     * StompConsumer constructor.
     * @param string $consumer_dir
     */
    public function __construct($consumer_dir = '')
    {
        $this->_consumerDir = $consumer_dir;
    }

    /**
     * onWorkerStart.
     */
    public function onWorkerStart()
    {
        if (!is_dir($this->_consumerDir)) {
            echo "Consumer directory {$this->_consumerDir} not exists\r\n";
            return;
        }
        $dir_iterator = new \RecursiveDirectoryIterator($this->_consumerDir);
        $iterator = new \RecursiveIteratorIterator($dir_iterator);
        foreach ($iterator as $file) {
            if (is_dir($file)) {
                continue;
            }
            $fileinfo = new \SplFileInfo($file);
            $ext = $fileinfo->getExtension();
            if ($ext === 'php') {
                $class = str_replace('/', "\\", substr(substr($file, strlen(base_path())), 0, -4));
                if (is_a($class, 'Webman\RedisQueue\Consumer', true)) {
                    $consumer = Container::get($class);
                    $connection_name = $consumer->connection ?? 'default';
                    $queue = $consumer->queue;
                    $connection = Client::connection($connection_name);
                    $connection->subscribe($queue, [$consumer, 'consume']);
                }
            }
        }

    }
}
```

`Webman\RedisQueue\Client` 类文件内容：
```php
<?php
/**
 * This file is part of webman.
 *
 * Licensed under The MIT License
 * For full copyright and license information, please see the MIT-LICENSE.txt
 * Redistributions of files must retain the above copyright notice.
 *
 * @author    walkor<walkor@workerman.net>
 * @copyright walkor<walkor@workerman.net>
 * @link      http://www.workerman.net/
 * @license   http://www.opensource.org/licenses/mit-license.php MIT License
 */
namespace Webman\RedisQueue;

use Workerman\RedisQueue\Client as RedisClient;

/**
 * Class RedisQueue
 * @package support
 *
 * Strings methods
 * @method static void send($queue, $data, $delay=0)
 */
class Client
{
    /**
     * @var Client[]
     */
    protected static $_connections = null;
    

    /**
     * @param string $name
     * @return RedisClient
     */
    public static function connection($name = 'default') {
        if (!isset(static::$_connections[$name])) {
            $config = config('redis_queue', config('plugin.webman.redis-queue.redis', []));
            if (!isset($config[$name])) {
                throw new \RuntimeException("RedisQueue connection $name not found");
            }
            $host = $config[$name]['host'];
            $options = $config[$name]['options'];
            $client = new RedisClient($host, $options);
            static::$_connections[$name] = $client;
        }
        return static::$_connections[$name];
    }

    /**
     * @param $name
     * @param $arguments
     * @return mixed
     */
    public static function __callStatic($name, $arguments)
    {
        return static::connection('default')->{$name}(... $arguments);
    }
}
```

`Workerman\RedisQueue\Client` 类文件内容：
```php
<?php
/**
 * This file is part of workerman.
 *
 * Licensed under The MIT License
 * For full copyright and license information, please see the MIT-LICENSE.txt
 * Redistributions of files must retain the above copyright notice.
 *
 * @author    walkor<walkor@workerman.net>
 * @copyright walkor<walkor@workerman.net>
 * @link      http://www.workerman.net/
 * @license   http://www.opensource.org/licenses/mit-license.php MIT License
 */
namespace Workerman\RedisQueue;

use RuntimeException;
use Workerman\Lib\Timer;
use Workerman\Redis\Client as Redis;

/**
 * Class Client
 * @package Workerman\RedisQueue
 */
class Client
{
    /**
     * Queue waiting for consumption
     */
    const QUEUE_WAITING = '{redis-queue}-waiting';

    /**
     * Queue with delayed consumption
     */
    const QUEUE_DELAYED = '{redis-queue}-delayed';

    /**
     * Queue with consumption failure
     */
    const QUEUE_FAILD = '{redis-queue}-failed';

    /**
     * @var Redis
     */
    protected $_redisSubscribe;

    /**
     * @var Redis
     */
    protected $_redisSend;

    /**
     * @var array
     */
    protected $_subscribeQueues = [];

    /**
     * @var array
     */
    protected $_options = [
        'retry_seconds' => 5,
        'max_attempts'  => 5,
        'auth'          => '',
        'db'            => 0,
    ];

    /**
     * Client constructor.
     * @param $address
     * @param array $options
     */
    public function __construct($address, $options = [])
    {
        $this->_redisSubscribe = new Redis($address, $options);
        $this->_redisSubscribe->brPoping = 0;
        $this->_redisSend = new Redis($address, $options);
        if (isset($options['auth'])) {
            $this->_redisSubscribe->auth($options['auth']);
            $this->_redisSend->auth($options['auth']);
        }
        if (isset($options['db'])) {
            $this->_redisSubscribe->select($options['db']);
            $this->_redisSend->select($options['db']);
        }
        $this->_options = array_merge($this->_options, $options);
    }

    /**
     * Send.
     *
     * @param $queue
     * @param $data
     * @param int $delay
     * @param callable $cb
     */
    public function send($queue, $data, $delay = 0, $cb = null)
    {
        static $_id = 0;
        $id = \microtime(true) . '.' . (++$_id);
        $now = time();
        $package_str = \json_encode([
            'id'       => $id,
            'time'     => $now,
            'delay'    => $delay,
            'attempts' => 0,
            'queue'    => $queue,
            'data'     => $data
        ]);
        if (\is_callable($delay)) {
            $cb = $delay;
            $delay = 0;
        }
        if ($cb) {
            $cb = function ($ret) use ($cb) {
               $cb((bool)$ret);
            };
            if ($delay == 0) {
                $this->_redisSend->lPush(static::QUEUE_WAITING . $queue, $package_str, $cb);
            } else {
                $this->_redisSend->zAdd(static::QUEUE_DELAYED, $now + $delay, $package_str, $cb);
            }
            return;
        }
        if ($delay == 0) {
            $this->_redisSend->lPush(static::QUEUE_WAITING . $queue, $package_str);
        } else {
            $this->_redisSend->zAdd(static::QUEUE_DELAYED, $now + $delay, $package_str);
        }
    }

    /**
     * Subscribe.
     *
     * @param string|array $queue
     * @param callable $callback
     */
    public function subscribe($queue, callable $callback)
    {
        $queue = (array)$queue;
        foreach ($queue as $q) {
            $redis_key = static::QUEUE_WAITING . $q;
            $this->_subscribeQueues[$redis_key] = $callback;
        }
        $this->pull();
    }

    /**
     * Unsubscribe.
     *
     * @param string|array $queue
     * @return void
     */
    public function unsubscribe($queue)
    {
        $queue = (array)$queue;
        foreach($queue as $q) {
            $redis_key = static::QUEUE_WAITING . $q;
            unset($this->_subscribeQueues[$redis_key]);
        }
    }

    /**
     * tryToPullDelayQueue.
     */
    protected function tryToPullDelayQueue()
    {
        static $retry_timer = 0;
        if ($retry_timer) {
            return;
        }
        $retry_timer = Timer::add(1, function () {
            $now = time();
            $options = ['LIMIT', 0, 128];
            $this->_redisSend->zrevrangebyscore(static::QUEUE_DELAYED, $now, '-inf', $options, function($items){
                if ($items === false) {
                    throw new RuntimeException($this->_redisSend->error());
                }
                foreach ($items as $package_str) {
                    $this->_redisSend->zRem(static::QUEUE_DELAYED, $package_str, function ($result) use ($package_str) {
                        if ($result !== 1) {
                            return;
                        }
                        $package = \json_decode($package_str, true);
                        if (!$package) {
                            $this->_redisSend->lPush(static::QUEUE_FAILD , $package_str);
                            return;
                        }
                        $this->_redisSend->lPush(static::QUEUE_WAITING . $package['queue'], $package_str);
                    });
                }
            });
        });
    }

    /**
     * pull.
     */
    public function pull()
    {
        $this->tryToPullDelayQueue();
        if (!$this->_subscribeQueues || $this->_redisSubscribe->brPoping) {
            return;
        }
        $cb = function($data) use (&$cb) {
            if ($data) {
                $this->_redisSubscribe->brPoping = 0;
                $redis_key = $data[0];
                $package_str = $data[1];
                $package = json_decode($package_str, true);
                if (!$package) {
                    $this->_redisSend->lPush(static::QUEUE_FAILD, $package_str);
                } else {
                    if (!isset($this->_subscribeQueues[$redis_key])) {
                        // 取消订阅，放回队列
                        $this->_redisSend->rPush($redis_key, $package_str);
                    } else {
                        $callback = $this->_subscribeQueues[$redis_key];
                        try {
                            \call_user_func($callback, $package['data']);
                        } catch (\Exception $e) {
                            if (++$package['attempts'] > $this->_options['max_attempts']) {
                                $package['error'] = (string) $e;
                                $this->fail($package);
                            } else {
                                $this->retry($package);
                            }
                        } catch (\Error $e) {
                            if (++$package['attempts'] > $this->_options['max_attempts']) {
                                $package['error'] = (string) $e;
                                $this->fail($package);
                            } else {
                                $this->retry($package);
                            }
                        }
                    }
                }
            }
            if ($this->_subscribeQueues) {
                $this->_redisSubscribe->brPoping = 1;
                Timer::add(0.000001, [$this->_redisSubscribe, 'brPop'], [\array_keys($this->_subscribeQueues), 1, $cb] ,false);
            }
        };
        $this->_redisSubscribe->brPoping = 1;
        $this->_redisSubscribe->brPop(\array_keys($this->_subscribeQueues), 1, $cb);
    }

    /**
     * @param $package
     */
    protected function retry($package)
    {
        $delay = time() + $this->_options['retry_seconds'] * ($package['attempts']);
        $this->_redisSend->zAdd(static::QUEUE_DELAYED, $delay, \json_encode($package));
    }

    /**
     * @param $package
     */
    protected function fail($package)
    {
        $this->_redisSend->lPush(static::QUEUE_FAILD , \json_encode($package));
    }

}
```

`Workerman\Redis\Client` 类文件内容：
```php
<?php
/**
 * This file is part of workerman.
 *
 * Licensed under The MIT License
 * For full copyright and license information, please see the LICENSE
 * Redistributions of files must retain the above copyright notice.
 *
 * @author    walkor<walkor@workerman.net>
 * @copyright walkor<walkor@workerman.net>
 * @link      http://www.workerman.net/
 * @license   http://www.opensource.org/licenses/mit-license.php MIT License
 */
namespace Workerman\Redis;

use Workerman\Connection\AsyncTcpConnection;
use Workerman\Lib\Timer;

/**
 * Class Client
 * @package Workerman\Redis
 *
 * Strings methods
 * @method static int append($key, $value, $cb = null)
 * @method static int bitCount($key, $cb = null)
 * @method static int decrBy($key, $value, $cb = null)
 * @method static string|bool get($key, $cb = null)
 * @method static int getBit($key, $offset, $cb = null)
 * @method static string getRange($key, $start, $end, $cb = null)
 * @method static string getSet($key, $value, $cb = null)
 * @method static int incrBy($key, $value, $cb = null)
 * @method static float incrByFloat($key, $value, $cb = null)
 * @method static array mGet(array $keys, $cb = null)
 * @method static array getMultiple(array $keys, $cb = null)
 * @method static bool setBit($key, $offset, $value, $cb = null)
 * @method static bool setEx($key, $ttl, $value, $cb = null)
 * @method static bool pSetEx($key, $ttl, $value, $cb = null)
 * @method static bool setNx($key, $value, $cb = null)
 * @method static string setRange($key, $offset, $value, $cb = null)
 * @method static int strLen($key, $cb = null)
 * Keys methods
 * @method static int del(...$keys, $cb = null)
 * @method static int unlink(...$keys, $cb = null)
 * @method static false|string dump($key, $cb = null)
 * @method static int exists(...$keys, $cb = null)
 * @method static bool expire($key, $ttl, $cb = null)
 * @method static bool pexpire($key, $ttl, $cb = null)
 * @method static bool expireAt($key, $timestamp, $cb = null)
 * @method static bool pexpireAt($key, $timestamp, $cb = null)
 * @method static array keys($pattern, $cb = null)
 * @method static bool|array scan($it, $cb = null)
 * @method static void migrate($host, $port, $keys, $dbIndex, $timeout, $copy = false, $replace = false, $cb = null)
 * @method static bool move($key, $dbIndex, $cb = null)
 * @method static string|int|bool object($information, $key, $cb = null)
 * @method static bool persist($key, $cb = null)
 * @method static string randomKey(, $cb = null)
 * @method static bool rename($srcKey, $dstKey, $cb = null)
 * @method static bool renameNx($srcKey, $dstKey, $cb = null)
 * @method static string type($key, $cb = null)
 * @method static int ttl($key, $cb = null)
 * @method static int pttl($key, $cb = null)
 * @method static void restore($key, $ttl, $value, $cb = null)
 * Hashes methods
 * @method static false|int hSet($key, $hashKey, $value, $cb = null)
 * @method static bool hSetNx($key, $hashKey, $value, $cb = null)
 * @method static false|string hGet($key, $hashKey, $cb = null)
 * @method static false|int hLen($key, $cb = null)
 * @method static false|int hDel($key, ...$hashKeys, $cb = null)
 * @method static array hKeys($key, $cb = null)
 * @method static array hVals($key, $cb = null)
 * @method static bool hExists($key, $hashKey, $cb = null)
 * @method static int hIncrBy($key, $hashKey, $value, $cb = null)
 * @method static float hIncrByFloat($key, $hashKey, $value, $cb = null)
 * @method static array hScan($key, $iterator, $pattern = '', $count = 0, $cb = null)
 * @method static int hStrLen($key, $hashKey, $cb = null)
 * Lists methods
 * @method static array blPop($keys, $timeout, $cb = null)
 * @method static array brPop($keys, $timeout, $cb = null)
 * @method static false|string bRPopLPush($srcKey, $dstKey, $timeout, $cb = null)
 * @method static false|string lIndex($key, $index, $cb = null)
 * @method static int lInsert($key, $position, $pivot, $value, $cb = null)
 * @method static false|string lPop($key, $cb = null)
 * @method static false|int lPush($key, ...$entries, $cb = null)
 * @method static false|int lPushx($key, $value, $cb = null)
 * @method static array lRange($key, $start, $end, $cb = null)
 * @method static false|int lRem($key, $value, $count, $cb = null)
 * @method static bool lSet($key, $index, $value, $cb = null)
 * @method static false|array lTrim($key, $start, $end, $cb = null)
 * @method static false|string rPop($key, $cb = null)
 * @method static false|string rPopLPush($srcKey, $dstKey, $cb = null)
 * @method static false|int rPush($key, ...$entries, $cb = null)
 * @method static false|int rPushX($key, $value, $cb = null)
 * @method static false|int lLen($key, $cb = null)
 * Sets methods
 * @method static int sAdd($key, $value, $cb = null)
 * @method static int sCard($key, $cb = null)
 * @method static array sDiff($keys, $cb = null)
 * @method static false|int sDiffStore($dst, $keys, $cb = null)
 * @method static false|array sInter($keys, $cb = null)
 * @method static false|int sInterStore($dst, $keys, $cb = null)
 * @method static bool sIsMember($key, $member, $cb = null)
 * @method static array sMembers($key, $cb = null)
 * @method static bool sMove($src, $dst, $member, $cb = null)
 * @method static false|string|array sPop($key, $count = 0, $cb = null)
 * @method static false|string|array sRandMember($key, $count = 0, $cb = null)
 * @method static int sRem($key, ...$members, $cb = null)
 * @method static array sUnion(...$keys, $cb = null)
 * @method static false|int sUnionStore($dst, ...$keys, $cb = null)
 * @method static false|array sScan($key, $iterator, $pattern = '', $count = 0, $cb = null)
 * Sorted sets methods
 * @method static array bzPopMin($keys, $timeout, $cb = null)
 * @method static array bzPopMax($keys, $timeout, $cb = null)
 * @method static int zAdd($key, $score, $value, $cb = null)
 * @method static int zCard($key, $cb = null)
 * @method static int zCount($key, $start, $end, $cb = null)
 * @method static double zIncrBy($key, $value, $member, $cb = null)
 * @method static int zinterstore($keyOutput, $arrayZSetKeys, $arrayWeights = [], $aggregateFunction = '', $cb = null)
 * @method static array zPopMin($key, $count, $cb = null)
 * @method static array zPopMax($key, $count, $cb = null)
 * @method static array zRange($key, $start, $end, $withScores = false, $cb = null)
 * @method static array zRangeByScore($key, $start, $end, $options = [], $cb = null)
 * @method static array zRevRangeByScore($key, $start, $end, $options = [], $cb = null)
 * @method static array zRangeByLex($key, $min, $max, $offset = 0, $limit = 0, $cb = null)
 * @method static int zRank($key, $member, $cb = null)
 * @method static int zRevRank($key, $member, $cb = null)
 * @method static int zRem($key, ...$members, $cb = null)
 * @method static int zRemRangeByRank($key, $start, $end, $cb = null)
 * @method static int zRemRangeByScore($key, $start, $end, $cb = null)
 * @method static array zRevRange($key, $start, $end, $withScores = false, $cb = null)
 * @method static double zScore($key, $member, $cb = null)
 * @method static int zunionstore($keyOutput, $arrayZSetKeys, $arrayWeights = [], $aggregateFunction = '', $cb = null)
 * @method static false|array zScan($key, $iterator, $pattern = '', $count = 0, $cb = null)
 * HyperLogLogs methods
 * @method static int pfAdd($key, $values, $cb = null)
 * @method static int pfCount($keys, $cb = null)
 * @method static bool pfMerge($dstKey, $srcKeys, $cb = null)
 * Geocoding methods
 * @method static int geoAdd($key, $longitude, $latitude, $member, ...$items, $cb = null)
 * @method static array geoHash($key, ...$members, $cb = null)
 * @method static array geoPos($key, ...$members, $cb = null)
 * @method static double geoDist($key, $members, $unit = '', $cb = null)
 * @method static int|array geoRadius($key, $longitude, $latitude, $radius, $unit, $options = [], $cb = null)
 * @method static array geoRadiusByMember($key, $member, $radius, $units, $options = [], $cb = null)
 * Streams methods
 * @method static int xAck($stream, $group, $arrMessages, $cb = null)
 * @method static string xAdd($strKey, $strId, $arrMessage, $iMaxLen = 0, $booApproximate = false, $cb = null)
 * @method static array xClaim($strKey, $strGroup, $strConsumer, $minIdleTime, $arrIds, $arrOptions = [], $cb = null)
 * @method static int xDel($strKey, $arrIds, $cb = null)
 * @method static mixed xGroup($command, $strKey, $strGroup, $strMsgId, $booMKStream = null, $cb = null)
 * @method static mixed xInfo($command, $strStream, $strGroup = null, $cb = null)
 * @method static int xLen($stream, $cb = null)
 * @method static array xPending($strStream, $strGroup, $strStart = 0, $strEnd = 0, $iCount = 0, $strConsumer = null, $cb = null)
 * @method static array xRange($strStream, $strStart, $strEnd, $iCount = 0, $cb = null)
 * @method static array xRead($arrStreams, $iCount = 0, $iBlock = null, $cb = null)
 * @method static array xReadGroup($strGroup, $strConsumer, $arrStreams, $iCount = 0, $iBlock = null, $cb = null)
 * @method static array xRevRange($strStream, $strEnd, $strStart, $iCount = 0, $cb = null)
 * @method static int xTrim($strStream, $iMaxLen, $booApproximate = null, $cb = null)
 * Pub/sub methods
 * @method static mixed publish($channel, $message, $cb = null)
 * @method static mixed pubSub($keyword, $argument = null, $cb = null)
 * Generic methods
 * @method static mixed rawCommand(...$commandAndArgs, $cb = null)
 * Transactions methods
 * @method static \Redis multi($cb = null)
 * @method static mixed exec($cb = null)
 * @method static mixed discard($cb = null)
 * @method static mixed watch($keys, $cb = null)
 * @method static mixed unwatch($keys, $cb = null)
 * Scripting methods
 * @method static mixed eval($script, $args = [], $numKeys = 0, $cb = null)
 * @method static mixed evalSha($sha, $args = [], $numKeys = 0, $cb = null)
 * @method static mixed script($command, ...$scripts, $cb = null)
 * @method static mixed client(...$args, $cb = null)
 * @method static null|string getLastError($cb = null)
 * @method static bool clearLastError($cb = null)
 * @method static mixed _prefix($value, $cb = null)
 * @method static mixed _serialize($value, $cb = null)
 * @method static mixed _unserialize($value, $cb = null)
 * Introspection methods
 * @method static bool isConnected($cb = null)
 * @method static mixed getHost($cb = null)
 * @method static mixed getPort($cb = null)
 * @method static false|int getDbNum($cb = null)
 * @method static false|double getTimeout($cb = null)
 * @method static mixed getReadTimeout($cb = null)
 * @method static mixed getPersistentID($cb = null)
 * @method static mixed getAuth($cb = null)
 */
class Client
{
    /**
     * @var AsyncTcpConnection
     */
    protected $_connection = null;

    /**
     * @var array
     */
    protected $_options = [];

    /**
     * @var string
     */
    protected $_address = '';

    /**
     * @var array
     */
    protected $_queue = [];

    /**
     * @var int
     */
    protected $_db = 0;

    /**
     * @var string|array
     */
    protected $_auth = null;

    /**
     * @var bool
     */
    protected $_waiting = true;

    /**
     * @var Timer
     */
    protected $_connectTimeoutTimer = null;

    /**
     * @var Timer
     */
    protected $_reconnectTimer = null;

    /**
     * @var callable
     */
    protected $_connectionCallback = null;

    /**
     * @var Timer
     */
    protected $_waitTimeoutTimer = null;

    /**
     * @var string
     */
    protected $_error = '';

    /**
     * @var bool
     */
    protected $_subscribe = false;

    /**
     * @var bool
     */
    protected $_firstConnect = true;

    /**
     * Client constructor.
     * @param $address
     * @param array $options
     * @param null $callback
     */
    public function __construct($address, $options = [], $callback = null)
    {
        if (!\class_exists('Protocols\Redis')) {
            \class_alias('Workerman\Redis\Protocols\Redis', 'Protocols\Redis');
        }
        $this->_address = $address;
        $this->_options = $options;
        $this->_connectionCallback = $callback;
        $this->connect();
        $timer = Timer::add(1, function () use (&$timer) {
            if (empty($this->_queue)) {
                return;
            }
            if ($this->_subscribe) {
                Timer::del($timer);
                return;
            }
            reset($this->_queue);
            $current_queue = current($this->_queue);
            $current_command = $current_queue[0][0];
            $ignore_first_queue = in_array($current_command, ['BLPOP', 'BRPOP']);
            $time = time();
            $timeout = isset($this->_options['wait_timeout']) ? $this->_options['wait_timeout'] : 600;
            $has_timeout = false;
            $first_queue = true;
            foreach ($this->_queue as $key => $queue) {
                if ($first_queue && $ignore_first_queue) {
                    $first_queue = false;
                    continue;
                }
                if ($time - $queue[1] > $timeout) {
                    $has_timeout = true;
                    unset($this->_queue[$key]);
                    $msg = "Workerman Redis Wait Timeout ($timeout seconds)";
                    if ($queue[2]) {
                        $this->_error = $msg;
                        \call_user_func($queue[2], false, $this);
                    } else {
                        echo new Exception($msg);
                    }
                }
            }
            if ($has_timeout && !$ignore_first_queue) {
                $this->closeConnection();
                $this->connect();
            }
        });
    }

    /**
     * connect
     */
    public function connect()
    {
        if ($this->_connection) {
            return;
        }

        $timeout = isset($this->_options['connect_timeout']) ? $this->_options['connect_timeout'] : 5;
        $context = isset($this->_options['context']) ? $this->_options['context'] : [];
        $this->_connection = new AsyncTcpConnection($this->_address, $context);

        $this->_connection->onConnect = function () {
            $this->_waiting = false;
            Timer::del($this->_connectTimeoutTimer);
            if ($this->_reconnectTimer) {
                Timer::del($this->_reconnectTimer);
                $this->_reconnectTimer = null;
            }

            if ($this->_db) {
                $this->_queue = \array_merge([[['SELECT', $this->_db], time(), null]], $this->_queue);
            }

            if ($this->_auth) {
                $this->_queue = \array_merge([[['AUTH', $this->_auth], time(), null]],  $this->_queue);
            }

            $this->_connection->onError = function ($connection, $code, $msg) {
                echo new \Exception("Workerman Redis Connection Error $code $msg");
            };
            $this->process();
            $this->_firstConnect && $this->_connectionCallback && \call_user_func($this->_connectionCallback, true, $this);
            $this->_firstConnect = false;
        };

        $time_start = microtime(true);
        $this->_connection->onError = function ($connection) use ($time_start) {
            $time = microtime(true) - $time_start;
            $msg = "Workerman Redis Connection Failed ($time seconds)";
            $this->_error = $msg;
            $exception = new \Exception($msg);
            if (!$this->_connectionCallback) {
                echo $exception;
                return;
            }
            $this->_firstConnect && \call_user_func($this->_connectionCallback, false, $this);
        };

        $this->_connection->onClose = function () use ($time_start) {
            $this->_subscribe = false;
            if ($this->_connectTimeoutTimer) {
                Timer::del($this->_connectTimeoutTimer);
            }
            if ($this->_reconnectTimer) {
                Timer::del($this->_reconnectTimer);
                $this->_reconnectTimer = null;
            }
            $this->closeConnection();
            if (microtime(true) - $time_start > 5) {
                $this->connect();
            } else {
                $this->_reconnectTimer = Timer::add(5, function () {
                    $this->connect();
                }, null, false);
            }
        };

        $this->_connection->onMessage = function ($connection, $data) {
            $this->_error = '';
            $this->_waiting = false;
            reset($this->_queue);
            $queue = current($this->_queue);
            $cb = $queue[2];
            $type = $data[0];
            if (!$this->_subscribe) {
                unset($this->_queue[key($this->_queue)]);
            }
            $success = $type === '-' || $type === '!' ? false : true;
            $exception = false;
            $result = false;
            if ($success) {
                $result = $data[1];
                if ($type === '+' && $result === 'OK') {
                    $result = true;
                }
            } else {
                $this->_error = $data[1];
            }
            if (!$cb) {
                $this->process();
                return;
            }
            // format.
            if (!empty($queue[3])) {
                $result = \call_user_func($queue[3], $result);
            }
            try {
                \call_user_func($cb, $result, $this);
            } catch (\Exception $exception) {
            }

            if ($type === '!') {
                $this->closeConnection();
                $this->connect();
            } else {
                $this->process();
            }
            if ($exception) {
                throw $exception;
            }
        };

        $this->_connectTimeoutTimer = Timer::add($timeout, function () use ($timeout) {
            $this->_connectTimeoutTimer = null;
            if ($this->_connection && $this->_connection->getStatus(false) === 'ESTABLISHED') {
                return;
            }
            $this->closeConnection();
            $this->_error = "Workerman Redis Connection to {$this->_address} timeout ({$timeout} seconds)";
            if ($this->_firstConnect && $this->_connectionCallback) {
                \call_user_func($this->_connectionCallback, false, $this);
            } else {
                echo $this->_error . "\n";
            }

        });
        $this->_connection->connect();
    }

    /**
     * process
     */
    public function process()
    {
        if (!$this->_connection || $this->_waiting || empty($this->_queue) || $this->_subscribe) {
            return;
        }
        \reset($this->_queue);
        $queue = \current($this->_queue);
        if ($queue[0][0] === 'SUBSCRIBE' || $queue[0][0] === 'PSUBSCRIBE') {
            $this->_subscribe = true;
        }
        $this->_waiting = true;
        $this->_connection->send($queue[0]);
        $this->_error = '';
    }

    /**
     * subscribe
     *
     * @param $channels
     * @param $cb
     */
    public function subscribe($channels, $cb)
    {
        $new_cb = function ($result) use ($cb) {
            if (!$result) {
                echo $this->error();
                return;
            }
            $response_type = $result[0];
            switch ($response_type) {
                case 'subscribe':
                    return;
                case 'message':
                    \call_user_func($cb, $result[1], $result[2], $this);
                    return;
                default:
                    echo 'unknow response type for subscribe. buffer:' . serialize($result) . "\n";
            }
        };
        $this->_queue[] = [['SUBSCRIBE', $channels], time(), $new_cb];
        $this->process();
    }

    /**
     * psubscribe
     *
     * @param $patterns
     * @param $cb
     */
    public function pSubscribe($patterns, $cb)
    {
        $new_cb = function ($result) use ($cb) {
            if (!$result) {
                echo $this->error();
                return;
            }
            $response_type = $result[0];
            switch ($response_type) {
                case 'psubscribe':
                    return;
                case 'pmessage':
                    \call_user_func($cb, $result[1], $result[2], $result[3], $this);
                    return;
                default:
                    echo 'unknow response type for psubscribe. buffer:' . serialize($result) . "\n";
            }
        };
        $this->_queue[] = [['PSUBSCRIBE', $patterns], time(), $new_cb];
        $this->process();
    }

    /**
     * select
     *
     * @param $db
     * @param null $cb
     */
    public function select($db, $cb = null)
    {
        $format = function ($result) use ($db) {
            $this->_db = $db;
            return $result;
        };
        $cb = $cb ? $cb : function(){};
        $this->_queue[] = [['SELECT', $db], time(), $cb, $format];
        $this->process();
    }

    /**
     * auth
     *
     * @param string|array $auth
     * @param null $cb
     */
    public function auth($auth, $cb = null)
    {
        $format = function ($result) use ($auth) {
            $this->_auth = $auth;
            return $result;
        };
        $cb = $cb ? $cb : function(){};
        $this->_queue[] = [['AUTH', $auth], time(), $cb, $format];
        $this->process();
    }

    /**
     * set
     *
     * @param $key
     * @param $value
     * @param null $cb
     * @return null
     */
    public function set($key, $value, $cb = null)
    {
        $args = func_get_args();
        if ($cb !== null && !\is_callable($cb)) {
            $timeout = $cb;
            $cb = null;
            if (\count($args) > 3) {
                $cb = $args[3];
            }
            $this->_queue[] = [['SETEX', $key, $timeout, $value], time(), $cb];
            $this->process();
            return null;
        }
        $this->_queue[] = [['SET', $key, $value], time(), $cb];
        $this->process();
    }

    /**
     * incr
     *
     * @param $key
     * @param null $cb
     * @return null
     */
    public function incr($key, $cb = null)
    {
        $args = func_get_args();
        if ($cb !== null && !\is_callable($cb)) {
            $num = $cb;
            $cb = null;
            if (\count($args) > 2) {
                $cb = $args[2];
            }
            $this->_queue[] = [['INCRBY', $key, $num], time(), $cb];
            $this->process();
            return null;
        }
        $this->_queue[] = [['INCR', $key], time(), $cb];
        $this->process();
    }


    /**
     * decr
     *
     * @param $key
     * @param null $cb
     * @return null
     */
    public function decr($key, $cb = null)
    {
        $args = func_get_args();
        if ($cb !== null && !\is_callable($cb)) {
            $num = $cb;
            $cb = null;
            if (\count($args) > 2) {
                $cb = $args[2];
            }
            $this->_queue[] = [['DECRBY', $key, $num], time(), $cb];
            $this->process();
            return null;
        }
        $this->_queue[] = [['DECR', $key], time(), $cb];
        $this->process();
    }

    /**
     * sort
     *
     * @param $key
     * @param $options
     * @param null $cb
     */
    function sort($key, $options, $cb = null)
    {
        $args = [];
        if (isset($options['sort'])) {
            $args[] = $options['sort'];
            unset($options['sort']);
        }

        foreach ($options as $op => $value) {
            $args[] = $op;
            if (!is_array($value)) {
                $args[] = $value;
                continue;
            }
            foreach ($value as $sub_value) {
                $args[] = $sub_value;
            }
        }
        \array_unshift($args, 'SORT', $key);
        $this->_queue[] = [$args, time(), $cb];
        $this->process();
    }

    /**
     * mSet
     *
     * @param array $array
     * @param null $cb
     */
    public function mSet(array $array, $cb = null)
    {
        $this->mapCb('MSET', $array, $cb);
    }

    /**
     * mSetNx
     *
     * @param array $array
     * @param null $cb
     */
    public function mSetNx(array $array, $cb = null)
    {
        $this->mapCb('MSETNX', $array, $cb);
    }

    /**
     * mapCb
     *
     * @param $command
     * @param array $array
     * @param $cb
     */
    protected function mapCb($command, array $array, $cb)
    {
        $args = [$command];
        foreach ($array as $key => $value) {
            $args[] = $key;
            $args[] = $value;
        }
        $this->_queue[] = [$args, time(), $cb];
        $this->process();
    }

    /**
     * hMSet
     *
     * @param $key
     * @param array $array
     * @param null $cb
     */
    public function hMSet($key, array $array, $cb = null)
    {
        $this->keyMapCb('HMSET', $key, $array, $cb);
    }

    /**
     * hMGet
     *
     * @param $key
     * @param array $array
     * @param null $cb
     */
    public function hMGet($key, array $array, $cb = null)
    {
        $format = function ($result) use ($array) {
            if (!is_array($result)) {
                return $result;
            }
            return \array_combine($array, $result);
        };
        $this->_queue[] = [['HMGET', $key, $array], time(), $cb, $format];
        $this->process();
    }

    /**
     * hGetAll
     *
     * @param $key
     * @param null $cb
     */
    public function hGetAll($key, $cb = null)
    {
        $format = function ($result) {
            if (!\is_array($result)) {
                return $result;
            }
            $return = [];
            $key = '';
            foreach ($result as $index => $item) {
                if ($index % 2 == 0) {
                    $key = $item;
                    continue;
                }
                $return[$key] = $item;
            }
            return $return;
        };
        $this->_queue[] = [['HGETALL', $key], time(), $cb, $format];
        $this->process();
    }

    /**
     * keyMapCb
     *
     * @param $command
     * @param $key
     * @param array $array
     * @param $cb
     */
    protected function keyMapCb($command, $key, array $array, $cb)
    {
        $args = [$command, $key];
        foreach ($array as $key => $value) {
            $args[] = $key;
            $args[] = $value;
        }
        $this->_queue[] = [$args, time(), $cb];
        $this->process();
    }

    /**
     * __call
     *
     * @param $method
     * @param $args
     */
    public function __call($method, $args)
    {
        $cb = null;
        if (\is_callable(end($args))) {
            $cb = array_pop($args);
        }

        \array_unshift($args, \strtoupper($method));
        $this->_queue[] = [$args, time(), $cb];
        $this->process();
    }

    /**
     * closeConnection
     */
    public function closeConnection()
    {
        if (!$this->_connection) {
            return;
        }
        $this->_subscribe = false;
        $this->_connection->onConnect = $this->_connection->onError = $this->_connection->onClose =
        $this->_connection->onMessge = null;
        $this->_connection->close();
        $this->_connection = null;
    }

    /**
     * error
     *
     * @return string
     */
    function error()
    {
        return $this->_error;
    }

    /**
     * close
     */
    public function close()
    {
        $this->closeConnection();
        $this->_queue = [];
    }

    /**
     * scan
     *
     * @throws Exception
     */
    public function scan()
    {
        throw new Exception('Not implemented');
    }

    /**
     * hScan
     *
     * @throws Exception
     */
    public function hScan()
    {
        throw new Exception('Not implemented');
    }

    /**
     * hScan
     *
     * @throws Exception
     */
    public function sScan()
    {
        throw new Exception('Not implemented');
    }

    /**
     * hScan
     *
     * @throws Exception
     */
    public function zScan()
    {
        throw new Exception('Not implemented');
    }
}
```

## 参考资料

redis队列插件 <https://www.workerman.net/plugin/12>




