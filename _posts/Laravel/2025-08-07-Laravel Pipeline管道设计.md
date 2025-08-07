---
layout: post
categories: Laravel
title: Laravel Pipeline管道设计
meta: Laravel Pipeline管道设计
---
* content
{:toc}

## 引言

```
> ps -ef | grep php
```

这个 Linux 命令就是一个管道命令。前面一条命令的结果交给后面一条命令来执行，就像一条管道一样让这个命令请求的结果向下流动，这就是管道模式的应用。在 PHP 设计模式中，责任链模式 很明显就是管道模式在 面向对象 语言中的应用。

Pipeline管道模式，也有人叫它装饰模式。应该说管道是装饰模式的一个变种，虽然思想都是一样的，但这个是闭包的版本，实现方式与传统装饰模式也不太一样。在laravel的源码中算是一个比较核心的设计模式了。

管道模式，或者说装饰模式的思想，就是在不改变原有程序的基础上，可以方便的在已有程序上添加新的功能。

管道模式一般是和过滤器一起使用的，什么是过滤器呢？其实就是我们要处理请求的那些中间方法，比如说上面命令中的 grep ，或者是 wc 、awk 这些的命令。大家其实很快就能发现，在 Laravel 框架中，我们的中间件就是一个个的过滤器。而我们要处理的数据，就是那个 Request 请求对象。

在说管道模式之前，让我们看一下 array_reduce() 这个函数 <https://www.php.net/manual/en/function.array-reduce.php>

```
array_reduce — 用回调函数迭代地将数组简化为单一的值

说明：

array_reduce(array $array, callable $callback, mixed $initial = null): mixed

array_reduce() 将回调函数 callback 迭代地作用到 array 数组中的每一个单元中，从而将数组简化为单一的值。

参数：

array
    输入的 array。

callback
    callback(mixed $carry, mixed $item): mixed

    carry
        携带上次迭代的返回值； 如果本次迭代是第一次，那么这个值是 initial。
    item
        携带了本次迭代的值。

initial
    如果指定了可选参数 initial，该参数将用作处理开始时的初始值，如果数组为空，则会作为最终结果返回。

返回值：

返回结果值。

如果数组为空，并且没有指定 initial 参数，array_reduce() 返回 null。
```

看一下下面的示例：
```php
<?php
function sum($carry, $item)
{
    $carry += $item;
    return $carry;
}

function product($carry, $item)
{
    $carry *= $item;
    return $carry;
}

$a = array(1, 2, 3, 4, 5);
$x = array();

var_dump(array_reduce($a, "sum")); // int(15), 因为：1+2+3+4+5
var_dump(array_reduce($a, "product", 10)); // int(1200), 因为：10*1*2*3*4*5
var_dump(array_reduce($x, "sum", "No data to reduce")); // string(17) "No data to reduce"
```

追踪过程再看一下
```php
<?php
function sum($carry, $item)
{
	var_dump('$carry-  ' . $carry);
	var_dump('$item-  ' . $item);
    $carry += $item;
	var_dump('$carry2  ' . $carry);
    return $carry;
}

$a = array(1, 2, 3, 4, 5);


var_dump(array_reduce($a, "sum")); 

string(9) "$carry-  "
string(9) "$item-  1"
string(10) "$carry2  1"
string(10) "$carry-  1"
string(9) "$item-  2"
string(10) "$carry2  3"
string(10) "$carry-  3"
string(9) "$item-  3"
string(10) "$carry2  6"
string(10) "$carry-  6"
string(9) "$item-  4"
string(11) "$carry2  10"
string(11) "$carry-  10"
string(9) "$item-  5"
string(11) "$carry2  15"
int(15)


function product($carry, $item)
{
	var_dump('$carry-  ' . $carry);
	var_dump('$item-  ' . $item);
    $carry *= $item;
	var_dump('$carry2  ' . $carry);
    return $carry;
}

var_dump(array_reduce($a, "product", 10)); 

string(11) "$carry-  10"
string(9) "$item-  1"
string(11) "$carry2  10"
string(11) "$carry-  10"
string(9) "$item-  2"
string(11) "$carry2  20"
string(11) "$carry-  20"
string(9) "$item-  3"
string(11) "$carry2  60"
string(11) "$carry-  60"
string(9) "$item-  4"
string(12) "$carry2  240"
string(12) "$carry-  240"
string(9) "$item-  5"
string(13) "$carry2  1200"
int(1200)


$x = array();
var_dump(array_reduce($x, "sum", "No data to reduce")); 

string(17) "No data to reduce"
```

## 举例类

再看一个实例：

![]({{site.baseurl}}/images/20250807/20250807171648.png)

约定一个接口规范：
```php
<?php 
interface RequestInterface
{
    // 我们之前讲过装饰者模式，这里是通过闭包函数实现
    // 通过之后实现类及调用就可以看出
    public static function handle(Closure $next);
}
```

遵循接口开始实现：
```php
class Request1 implements RequestInterface
{
    public static function handle(Closure $next)
    {
        echo "Request1 Begin." . "<br />";
        $next();
        echo "Request1 End." . "<br />";
    }
}

class Request2 implements RequestInterface
{
    public static function handle(Closure $next)
    {
        echo "Request2 Begin." . "<br />";
        $next();
        echo "Request2 End." . "<br />";
    }
}

class Request3 implements RequestInterface
{
    public static function handle(Closure $next)
    {
        echo "Request3 Begin." . "<br />";
        $next();
        echo "Request3 End." . "<br />";
    }
}

class Request4 implements RequestInterface
{
    public static function handle(Closure $next)
    {
        echo "Request4 Begin." . "<br />";
        $next();
        echo "Request4 End." . "<br />";
    }
}
```

客户端来发出请求：
```php
class Client
{
    // 这里包含了所有的请求
    private $pipes = [
        'Request1',
        'Request2',
        'Request3',
        'Request4',
    ];

    // 这里就是思维导图中默认返回的匿名回调函数
    private function defaultClosure()
    {
        return function () {
            echo '请求处理中...' . "<br />";
        };
    }

    // 这里就是整个请求处理管道的关键
    private function getSlice()
    {
        return function ($stack, $pipe) {
            // 返回一个闭包
            return function () use ($stack, $pipe) {
                return $pipe::handle($stack);
            };
        };
    }

    // 这里是负责发起请求处理
    public function then()
    {
        call_user_func(array_reduce($this->pipes, $this->getSlice(), $this->defaultClosure()));
    }
}

$worker = new Client();
$worker->then();
```

运行结果：
```
Request4 Begin.
Request3 Begin.
Request2 Begin.
Request1 Begin.
请求处理中...
Request1 End.
Request2 End.
Request3 End.
Request4 End.
```

getSlice() 循环闭包返回值也是一个闭包，在不断的循环中，闭包函数就像洋葱一样，一层一层不断包裹。

最终array_reduce返回一个匿名函数：
```php
function () {
    return Request4::handle(function () {
        return Request3::handle(function () {
            return Request2::handle(function () {
                return Reuqest1::handle(function () {
                    echo '请求处理中...' . "<br />";
                });
            });
        });
    });
}
```

当调用call_user_func时就是执行array_reduce返回的匿名函数。

## Laravel实例

Laravel框架源码分析之Pipeline管道设计 <https://zhuanlan.zhihu.com/p/669130742>

先看下Laravel框架中Pipeline类的代码：
```php
<?php

namespace Illuminate\Pipeline;

use Closure;
use Exception;
use Illuminate\Contracts\Container\Container;
use Illuminate\Contracts\Pipeline\Pipeline as PipelineContract;
use RuntimeException;
use Symfony\Component\Debug\Exception\FatalThrowableError;
use Throwable;

class Pipeline implements PipelineContract
{
    /**
     * The container implementation.
     *
     * @var \Illuminate\Contracts\Container\Container
     */
    protected $container;

    /**
     * The object being passed through the pipeline.
     *
     * @var mixed
     */
    protected $passable;

    /**
     * The array of class pipes.
     *
     * @var array
     */
    protected $pipes = [];

    /**
     * The method to call on each pipe.
     *
     * @var string
     */
    protected $method = 'handle';

    /**
     * Create a new class instance.
     *
     * @param  \Illuminate\Contracts\Container\Container|null  $container
     * @return void
     */
    public function __construct(Container $container = null)
    {
        $this->container = $container;
    }

    /**
     * Set the object being sent through the pipeline.
     *
     * @param  mixed  $passable
     * @return $this
     */
    public function send($passable)
    {
        $this->passable = $passable;

        return $this;
    }

    /**
     * Set the array of pipes.
     *
     * @param  array|mixed  $pipes
     * @return $this
     */
    public function through($pipes)
    {
        $this->pipes = is_array($pipes) ? $pipes : func_get_args();

        return $this;
    }

    /**
     * Set the method to call on the pipes.
     *
     * @param  string  $method
     * @return $this
     */
    public function via($method)
    {
        $this->method = $method;

        return $this;
    }

    /**
     * Run the pipeline with a final destination callback.
     *
     * @param  \Closure  $destination
     * @return mixed
     */
    public function then(Closure $destination)
    {
        $pipeline = array_reduce(
            array_reverse($this->pipes()), $this->carry(), $this->prepareDestination($destination)
        );

        return $pipeline($this->passable);
    }

    /**
     * Run the pipeline and return the result.
     *
     * @return mixed
     */
    public function thenReturn()
    {
        return $this->then(function ($passable) {
            return $passable;
        });
    }

    /**
     * Get the final piece of the Closure onion.
     *
     * @param  \Closure  $destination
     * @return \Closure
     */
    protected function prepareDestination(Closure $destination)
    {
        return function ($passable) use ($destination) {
            try {
                return $destination($passable);
            } catch (Exception $e) {
                return $this->handleException($passable, $e);
            } catch (Throwable $e) {
                return $this->handleException($passable, new FatalThrowableError($e));
            }
        };
    }

    /**
     * Get a Closure that represents a slice of the application onion.
     *
     * @return \Closure
     */
    protected function carry()
    {
        return function ($stack, $pipe) {
            return function ($passable) use ($stack, $pipe) {
                try {
                    if (is_callable($pipe)) {
                        // If the pipe is a callable, then we will call it directly, but otherwise we
                        // will resolve the pipes out of the dependency container and call it with
                        // the appropriate method and arguments, returning the results back out.
                        return $pipe($passable, $stack);
                    } elseif (! is_object($pipe)) {
                        [$name, $parameters] = $this->parsePipeString($pipe);

                        // If the pipe is a string we will parse the string and resolve the class out
                        // of the dependency injection container. We can then build a callable and
                        // execute the pipe function giving in the parameters that are required.
                        $pipe = $this->getContainer()->make($name);

                        $parameters = array_merge([$passable, $stack], $parameters);
                    } else {
                        // If the pipe is already an object we'll just make a callable and pass it to
                        // the pipe as-is. There is no need to do any extra parsing and formatting
                        // since the object we're given was already a fully instantiated object.
                        $parameters = [$passable, $stack];
                    }

                    $carry = method_exists($pipe, $this->method)
                                    ? $pipe->{$this->method}(...$parameters)
                                    : $pipe(...$parameters);

                    return $this->handleCarry($carry);
                } catch (Exception $e) {
                    return $this->handleException($passable, $e);
                } catch (Throwable $e) {
                    return $this->handleException($passable, new FatalThrowableError($e));
                }
            };
        };
    }

    /**
     * Parse full pipe string to get name and parameters.
     *
     * @param  string  $pipe
     * @return array
     */
    protected function parsePipeString($pipe)
    {
        [$name, $parameters] = array_pad(explode(':', $pipe, 2), 2, []);

        if (is_string($parameters)) {
            $parameters = explode(',', $parameters);
        }

        return [$name, $parameters];
    }

    /**
     * Get the array of configured pipes.
     *
     * @return array
     */
    protected function pipes()
    {
        return $this->pipes;
    }

    /**
     * Get the container instance.
     *
     * @return \Illuminate\Contracts\Container\Container
     *
     * @throws \RuntimeException
     */
    protected function getContainer()
    {
        if (! $this->container) {
            throw new RuntimeException('A container instance has not been passed to the Pipeline.');
        }

        return $this->container;
    }

    /**
     * Handle the value returned from each pipe before passing it to the next.
     *
     * @param  mixed  $carry
     * @return mixed
     */
    protected function handleCarry($carry)
    {
        return $carry;
    }

    /**
     * Handle the given exception.
     *
     * @param  mixed  $passable
     * @param  \Exception  $e
     * @return mixed
     *
     * @throws \Exception
     */
    protected function handleException($passable, Exception $e)
    {
        throw $e;
    }
}
```

最典型的使用就是中间件，中间件就是一个管道，每个中间件都是一个管道，最后一个管道是控制器，控制器返回的结果是一个响应，响应会被每个中间件处理，最后返回给用户。

看一下请求的处理：
```php
<?php

namespace Illuminate\Foundation\Http;

class Kernel implements KernelContract
{
    /**省略若干**/

    /**
     * Handle an incoming HTTP request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return \Illuminate\Http\Response
     */
    public function handle($request)
    {
        try {
            $request->enableHttpMethodParameterOverride();

            $response = $this->sendRequestThroughRouter($request);  // 调用下面的方法
        } catch (Exception $e) {
            $this->reportException($e);

            $response = $this->renderException($request, $e);
        } catch (Throwable $e) {
            $this->reportException($e = new FatalThrowableError($e));

            $response = $this->renderException($request, $e);
        }

        $this->app['events']->dispatch(
            new RequestHandled($request, $response)
        );

        return $response;
    }

    /**
     * Send the given request through the middleware / router.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return \Illuminate\Http\Response
     */
    protected function sendRequestThroughRouter($request)
    {
        $this->app->instance('request', $request);

        Facade::clearResolvedInstance('request');

        $this->bootstrap();

        return (new Pipeline($this->app))
                    ->send($request)
                    ->through($this->app->shouldSkipMiddleware() ? [] : $this->middleware)
                    ->then($this->dispatchToRouter());
    }

    /**省略若干**/
}
```

`$this->middleware` 在 `app/Http/Kernel.php` 下：

```php
/**
 * The application's global HTTP middleware stack.
 *
 * These middleware are run during every request to your application.
 *
 * @var array
 */
protected $middleware = [
    \App\Http\Middleware\TrustProxies::class,
    \App\Http\Middleware\CheckForMaintenanceMode::class,
    \Illuminate\Foundation\Http\Middleware\ValidatePostSize::class,
    \App\Http\Middleware\TrimStrings::class,
    \Illuminate\Foundation\Http\Middleware\ConvertEmptyStringsToNull::class,
];
```

看一下 `\App\Http\Middleware\TrimStrings::class` 的代码：
```php
<?php

namespace App\Http\Middleware;

use Illuminate\Foundation\Http\Middleware\TrimStrings as Middleware;

class TrimStrings extends Middleware
{
    /**
     * The names of the attributes that should not be trimmed.
     *
     * @var array
     */
    protected $except = [
        'password',
        'password_confirmation',
    ];
}
```

父类：
```php
<?php

namespace Illuminate\Foundation\Http\Middleware;

class TrimStrings extends TransformsRequest
{
    /**
     * The attributes that should not be trimmed.
     *
     * @var array
     */
    protected $except = [
        //
    ];

    /**
     * Transform the given value.
     *
     * @param  string  $key
     * @param  mixed  $value
     * @return mixed
     */
    protected function transform($key, $value)
    {
        if (in_array($key, $this->except, true)) {
            return $value;
        }

        return is_string($value) ? trim($value) : $value;
    }
}
```

父类：
```php
<?php

namespace Illuminate\Foundation\Http\Middleware;

use Closure;
use Symfony\Component\HttpFoundation\ParameterBag;

class TransformsRequest
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        $this->clean($request);

        return $next($request);
    }

    /**
     * Clean the request's data.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return void
     */
    protected function clean($request)
    {
        $this->cleanParameterBag($request->query);

        if ($request->isJson()) {
            $this->cleanParameterBag($request->json());
        } elseif ($request->request !== $request->query) {
            $this->cleanParameterBag($request->request);
        }
    }

    /**
     * Clean the data in the parameter bag.
     *
     * @param  \Symfony\Component\HttpFoundation\ParameterBag  $bag
     * @return void
     */
    protected function cleanParameterBag(ParameterBag $bag)
    {
        $bag->replace($this->cleanArray($bag->all()));
    }

    /**
     * Clean the data in the given array.
     *
     * @param  array  $data
     * @param  string  $keyPrefix
     * @return array
     */
    protected function cleanArray(array $data, $keyPrefix = '')
    {
        return collect($data)->map(function ($value, $key) use ($keyPrefix) {
            return $this->cleanValue($keyPrefix.$key, $value);
        })->all();
    }

    /**
     * Clean the given value.
     *
     * @param  string  $key
     * @param  mixed  $value
     * @return mixed
     */
    protected function cleanValue($key, $value)
    {
        if (is_array($value)) {
            return $this->cleanArray($value, $key.'.');
        }

        return $this->transform($key, $value);
    }

    /**
     * Transform the given value.
     *
     * @param  string  $key
     * @param  mixed  $value
     * @return mixed
     */
    protected function transform($key, $value)
    {
        return $value;
    }
}
```
















## 参考资料

Laravel框架源码分析之Pipeline管道设计 <https://zhuanlan.zhihu.com/p/669130742>

一文了解Laravel中的Pipeline（管道） <https://www.php.cn/faq/497864.html>

Laravel系列6.4 管道过滤器 <https://cloud.tencent.com/developer/article/2232112>

laravel5.5源码笔记（五、Pipeline管道模式） <https://www.cnblogs.com/wyycc/p/9986757.html>

深度挖掘 Laravel 生命周期 <https://learnku.com/articles/10421/depth-mining-of-laravel-life-cycle>


