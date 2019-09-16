---
layout: post
categories: Yii2
title: Yii2 bootstrap加载过程
meta: bootstrap加载过程
---
* content
{:toc}

### 正文

一般我们会在Yii2的配置文件中写入bootstrap预加载组件配置，以记录log日志，如config文件中有：
```
'bootstrap' => ['log'],
'components' => [
    'log' => [
        'targets' => [
            'kafka' =>
                [
                    'class' => 'common\lib\LKafkaTarget',
                ],
        ],
    ],
    ...
],
```

我们追踪一下过程。

运行项目入口文件index.php中有：
```
$config = yii\helpers\ArrayHelper::merge(
    require(__DIR__ . '/../../common/config/main.php'),
    require(__DIR__ . '/../../common/config/main-prod.php'),
    require(__DIR__ . '/../config/main.php'),
    require(__DIR__ . '/../config/main-prod.php')
);

$application = new yii\web\Application($config);
$application->run();
```

yii\web\Application实例化会调用父类yii\base\Application中的 __construct($config = [])：
```
public function __construct($config = [])
{
    Yii::$app = $this;
    
    static::setInstance($this);

    $this->state = self::STATE_BEGIN;

    $this->preInit($config);

    $this->registerErrorHandler($config);

    Component::__construct($config);
}
```

Component::__construct($config)会调用父类yii\base\Object中的__construct()方法：
```
public function __construct($config = [])
{
    if (!empty($config)) {
        Yii::configure($this, $config);
    }
    $this->init();
}
```

Yii::configure($this, $config)会为当前对象yii\web\Application进行配置：
```
public static function configure($object, $properties)
{
    foreach ($properties as $name => $value) {
        $object->$name = $value;
    }

    return $object;
}
```

可以看到会把参数配置中的 bootstrap 赋值给对象的 yii\base\Application 的 public $bootstrap = [] 以备将来使用。 

上面 $this 指向当前对象yii\web\Application，调用的是父类的yii\base\Application的init()方法：
```
public function init()
{
    $this->state = self::STATE_INIT;
    $this->bootstrap();
}
```

这里可以看到bootstrap()方法，也在yii\base\Applicatio中：
```
protected function bootstrap()
{
    if ($this->extensions === null) {
        $file = Yii::getAlias('@vendor/yiisoft/extensions.php');
        $this->extensions = is_file($file) ? include($file) : [];
    }
    foreach ($this->extensions as $extension) {
        if (!empty($extension['alias'])) {
            foreach ($extension['alias'] as $name => $path) {
                Yii::setAlias($name, $path);
            }
        }
        if (isset($extension['bootstrap'])) {
            $component = Yii::createObject($extension['bootstrap']);
            if ($component instanceof BootstrapInterface) {
                Yii::trace('Bootstrap with ' . get_class($component) . '::bootstrap()', __METHOD__);
                $component->bootstrap($this);
            } else {
                Yii::trace('Bootstrap with ' . get_class($component), __METHOD__);
            }
        }
    }

    foreach ($this->bootstrap as $class) {
        $component = null;
        if (is_string($class)) {
            if ($this->has($class)) {
                $component = $this->get($class);
            } elseif ($this->hasModule($class)) {
                $component = $this->getModule($class);
            } elseif (strpos($class, '\\') === false) {
                throw new InvalidConfigException("Unknown bootstrapping component ID: $class");
            }
        }
        if (!isset($component)) {
            $component = Yii::createObject($class);
        }

        if ($component instanceof BootstrapInterface) {
            Yii::trace('Bootstrap with ' . get_class($component) . '::bootstrap()', __METHOD__);
            $component->bootstrap($this);
        } else {
            Yii::trace('Bootstrap with ' . get_class($component), __METHOD__);
        }
    }
}
```

这里可以看到$this->bootstrap属性，就是上面参数配置中赋的值。

也使用到了服务定位，has()、get()方法等。在yii\di\ServiceLocator中：
```
public function has($id, $checkInstance = false)
{
    return $checkInstance ? isset($this->_components[$id]) : isset($this->_definitions[$id]);
}

public function get($id, $throwException = true)
{
    if (isset($this->_components[$id])) {
        return $this->_components[$id];
    }

    if (isset($this->_definitions[$id])) {
        $definition = $this->_definitions[$id];
        if (is_object($definition) && !$definition instanceof Closure) {
            return $this->_components[$id] = $definition;
        } else {
            return $this->_components[$id] = Yii::createObject($definition);
        }
    } elseif ($throwException) {
        throw new InvalidConfigException("Unknown component ID: $id");
    } else {
        return null;
    }
}
```

在Yii::configure()时给$this->_components、$this->_definitions属性赋了值，其中就包括log配置，这里是实例化log组件，
Yii::createObject($definition)。

<br/><br/><br/><br/><br/>
### 参考资料


