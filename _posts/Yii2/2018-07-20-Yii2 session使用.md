---
layout: post
categories: Yii2
title: Yii2 session使用
meta: session使用
---
* content
{:toc}

## 引言

### Session说明

HTTP协议是无状态的，服务器无法区分哪些请求来自哪些客户端，或者客户端是处于连接状态还是断开状态，所以需要标识。

什么是Cookie？Cookie是网站区分不同访问者的身份而存储在客户端上的数据，是一个文本文件。
Cookie是某些网站为了辨别用户身份、进行 Session 跟踪而储存在用户本地终端上的数据（通常经过加密）。

什么是Session？Session 技术是依赖 Cookie 技术的服务器端的数据存储技术。作用：解决了一个用户的不同请求的数据共享问题。
在用户访问网页与服务器断开连接的一个时间段内，Session可以跟踪用户的状态
(如是否已登录，这样用户切换网页后也可以知道该用户需不需要登录)。
Session是一种会话，存储在服务端，它记录会话信息，使得客户端与服务端的会话得以保持。

Session和Cookie区别是什么？
1. Cookie是通过扩展http协议实现的，而Session是一种类似散列表的形式保存信息；
2. Cookie是保存在【客户端】的，而Session是保存在【服务端】的。

一、Cookie 是通过扩展HTTP协议实现的

Cookie 主要包括：名字、值、过期时间、路径和域。

如果Cookie不设置生命周期，则以浏览器关闭而关闭，这种Cookie一般存储在内存而不是硬盘上。
若设置了生命周期则相反，不随浏览器的关闭而消失，这些Cookie仍然有效直到超过设定的过期时间。

二、Session 一种类似散列表的形式保存信息

当程序需要为某个客户端的请求创建一个Session时，服务器首先检查这个客户端的请求里是否已包含了一个Session标识（称为session_id）。

如果已包含则说明以前已经为此客户端创建过Session，服务器就按照session_id把这个Session检索出来使用（检索不到，会新建一个），
如果客户端请求不包含session_id，则为此客户端创建一个Session并且生成一个与此Session相关联的session_id，
session_id的值应该是一个既不会重复，又不容易被找到规律以仿造的字符串，
**这个session_id将在本次响应中返回给客户端保存**（如通过URL）。

保存这个session_id的方式可以采用Cookie，这样在交互过程中浏览器可以自动的按照规则把这个标识发送给服务器。
一般这个Cookie的名字都是类似于SEEESIONID。

但Cookie可以被人为的禁止，则必须有其他机制以便在Cookie被禁止时仍然**能够把session_id传递回服务器**。

优缺点：
1. Cookie数据存放在客户的浏览器上，Session数据放在服务器上。
2. Cookie不是很安全，别人可以分析存放在本地的COOKIE并进行COOKIE欺骗，考虑到安全应当使用Session（如果认为自己的加密足够复杂，可以不用Session，不过不建议）。
3. Session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能，考虑到减轻服务器性能方面，应当使用COOKIE。
4. 单个Cookie保存的数据不能超过4K，很多浏览器都限制一个站点最多保存20个Cookie。
5. 建议：将登陆信息等重要信息存放为SESSION；其他信息如果需要保留，可以放在COOKIE中

### Cookie篡改

Cookie篡改是各种旨在操纵或伪造HTTP Cookie的攻击的通用术语。 根据估计的不同，cookie篡改可能是cookie劫持、暴露敏感信息或者拿走受害者的账户。让我们看看那些攻击牵涉到cookie篡改和如何预防攻击。

**什么是cookie篡改？**

在我们深入探讨前，我们先快速地理清一下术语。狭义上，cookie篡改攻击指直接修改已存在的cookie值。cookies只是存储在用户浏览器的文本值，因此，如果没有额外的防护措施，你可轻松地通过手动或者Javascript的document.cookie属性修改它们。幸运的是这样基础的攻击很少可能出现在现代web开发中，因为现代web开发中会话管理和其它涉及cookie操作的通常框架层面完成。

更广泛地说，“cookie篡改”这术语通常指所有的cookie相关的攻击，即使不涉及到篡改cookie本身。例如，一个容易受到攻击的程序可能允许恶意用户去发现cookie值或者设置新cookie去执行各种攻击。

**用户端cookie篡改**

如你在你的浏览器打开开发者工具面板，你可以查看和手动修改当前设置的cookie。一个脆弱的站点或者web应用可能把存储敏感状态信息直接在cookies，例如，是否允许管理员访问的标志。那样的话在浏览器手动改cookie的名称和值再刷新页面可能足以获得提升的权限。在web框架时代，很难找到一个生产站点或程序以为这种方式手动编码攻击，但理论上是可能的。

对攻击者更有用的是JavaScript提供的document.cookie属性，用于在脚本中处理cookies。如果一个敏感的cookie，例如会话session，没有额外的httponly标记，在一个成功的跨站点脚本（XSS）攻击后可由其读取和修改。

**基于Cookie的会话攻击**

在广义上说，cookie篡改意味着任何形式的cookie操纵，通常以会话cookies为目标。HTTP是一个无状态的协议，因此程序使用cookie来保持会话信息和其它数据在用户的计算机中。会话标识符是最有价值的数据存储在应用程序cookies中，因为它为会话劫持和相关的攻击开辟了道路。

所有基于Cookie的会话攻击针对用户会话都有相同的基本目标：骗web服务器认为攻击者是合法用户。这是一个快速的总结：

* Session劫持：也叫cookie劫持或者side-jacking，这是一种当用户登录到特定站点时，攻击者接管用户会话的攻击。攻击依赖于攻击者对当前会话cookie的了解。
* Session欺骗：相似于会话劫持，但是是在用户未登录时执行。攻击者使用窃取的或者伪造的会话tokens来发起一新的会话，在不需要用户交互的情况下冒充合法用户。
* Session fixation：攻击者提供的会话标识符（例如，在钓鱼邮件中），并欺骗用户使用此标识符登录易受攻击的站点。 如果站点允许，则攻击者可以使用已知会话标识符劫持用户会话。 

不管使用那种方法，会话被成功地接管都可能带来灾难性的后果。根据目标站点或者应用程序，攻击者可能能够窃取机密的用户信息，例如登录凭据或个人信息，或执行不良操作，例如转帐资金或添加新的用户帐户以供将来访问。

**预防cookie篡改攻击**

与众多安全漏洞一样，大多数与Cookie相关的攻击的共同点输入验证不足和信任了HTTP请求中的用户控制的数据。适当的cookie设置是Web应用程序安全的基本最佳实践，有许多安全措施可以最大程度地减少Cookie篡改攻击的风险：

* 使用正确的Cookie标志和属性：有几种方法可以减少攻击者对Cookie的访问。通过设置httpOnly标记，你可以确保脚本无法访问Cookie。特别推荐对于会话cookies设置secure标记以确保仅通过HTTPS发送cookie。仔细选择domain属性的值还可以最大程度地减少cookie滥用-有关更多信息，请参见我们的cookie安全性白皮书。 
* 使用唯一且安全的会话cookies：会话标识符应该是攻击者不可访问的，并且是随机生成的，因此无法猜测或暴力破解(brute-force) 。会话关闭后，它们也应该不可用。请参阅我们关于跨站点cookie操作的文章，以获取针对PHP中的会话标识符的攻击的实际示例。 
* 单个任务使用各自的cookie：使用多用途cookie可能很诱人，但这会使工作流程复杂化并可能带来安全风险。 特别是，会话cookie绝对不能用作反CSRF tokens或密码重置令牌。
* 使用适当的会话管理：cookie安全性对于会话管理来说特别重要，因为一个错误可能会打开攻击之门，避免简单的错误，跟随着最佳实践的第一步是使用受信任且成熟的框架中的内置会话管理功能，而不是重新造轮子（并且不得不对其进行维护）。请参阅我们的文章，以了解为什么选择Web框架对安全性至关重要。


参阅 <https://zhuanlan.zhihu.com/p/374624028>

参阅 <https://www.invicti.com/blog/web-security/understanding-cookie-poisoning-attacks/>

关于网站安全，参阅 <https://www.163.com/dy/article/GC4RPJ000531PCHX.html>

## 正文

session使用举例：

```
$session = \Yii::$app->session;

(一)、添加一个session
$session->set('name_string','value');
$session->set('name_array',[1,2,3]);  

(二)、读取一个session
$session->get('name_string');
$session->get('name_array');  

(三)、删除一个session
$session->remove('name_array');  

(四)、删除所有session
$session->removeAll();

(五)、判断session是否已定义
$session->has('name_string')
```

如果Yii::$app->session这样用报错： Unknown component ID: session

可以这样用：

```
use yii\web\Session;

$session = new Session;
$session->open();
// 下面和上面一样用

```

不过还是建议用component组件的方式来用，在config配置文件中components数组下写入：
```
'session' => [
    'class' => 'yii\web\Session',
    'name' => 'advanced-backend',  // 可以不写，yii2默认命名
    'timeout' => 86400,
],
```

## 完整示例

Yii2项目中配置如下：
```
'session' => [
    'class' => 'yii\web\Session',
    'name' => 'demo-backend',
],
'user' => [
    'identityClass' => 'backend\models\User',
    'enableAutoLogin' => true,
    'identityCookie' => ['name' => '_identity-demo', 'httpOnly' => true],
],
```

登录后，再请求时：

![]({{site.baseurl}}/images/20230707/20230707163642.png)

![]({{site.baseurl}}/images/20230707/20230707163706.png)

## 源码

`yii\web\Session` 源码：
```php
<?php
/**
 * @link http://www.yiiframework.com/
 * @copyright Copyright (c) 2008 Yii Software LLC
 * @license http://www.yiiframework.com/license/
 */

namespace yii\web;

use Yii;
use yii\base\Component;
use yii\base\InvalidArgumentException;
use yii\base\InvalidConfigException;

/**
 * Session provides session data management and the related configurations.
 *
 * Session is a Web application component that can be accessed via `Yii::$app->session`.
 *
 * To start the session, call [[open()]]; To complete and send out session data, call [[close()]];
 * To destroy the session, call [[destroy()]].
 *
 * Session can be used like an array to set and get session data. For example,
 *
 * ```php
 * $session = new Session;
 * $session->open();
 * $value1 = $session['name1'];  // get session variable 'name1'
 * $value2 = $session['name2'];  // get session variable 'name2'
 * foreach ($session as $name => $value) // traverse all session variables
 * $session['name3'] = $value3;  // set session variable 'name3'
 * ```
 *
 * Session can be extended to support customized session storage.
 * To do so, override [[useCustomStorage]] so that it returns true, and
 * override these methods with the actual logic about using custom storage:
 * [[openSession()]], [[closeSession()]], [[readSession()]], [[writeSession()]],
 * [[destroySession()]] and [[gcSession()]].
 *
 * Session also supports a special type of session data, called *flash messages*.
 * A flash message is available only in the current request and the next request.
 * After that, it will be deleted automatically. Flash messages are particularly
 * useful for displaying confirmation messages. To use flash messages, simply
 * call methods such as [[setFlash()]], [[getFlash()]].
 *
 * For more details and usage information on Session, see the [guide article on sessions](guide:runtime-sessions-cookies).
 *
 * @property array $allFlashes Flash messages (key => message or key => [message1, message2]). This property
 * is read-only.
 * @property string $cacheLimiter Current cache limiter. This property is read-only.
 * @property array $cookieParams The session cookie parameters. This property is read-only.
 * @property int $count The number of session variables. This property is read-only.
 * @property string $flash The key identifying the flash message. Note that flash messages and normal session
 * variables share the same name space. If you have a normal session variable using the same name, its value will
 * be overwritten by this method. This property is write-only.
 * @property float $gCProbability The probability (percentage) that the GC (garbage collection) process is
 * started on every session initialization.
 * @property bool $hasSessionId Whether the current request has sent the session ID.
 * @property string $id The current session ID.
 * @property bool $isActive Whether the session has started. This property is read-only.
 * @property SessionIterator $iterator An iterator for traversing the session variables. This property is
 * read-only.
 * @property string $name The current session name.
 * @property string $savePath The current session save path, defaults to '/tmp'.
 * @property int $timeout The number of seconds after which data will be seen as 'garbage' and cleaned up. The
 * default value is 1440 seconds (or the value of "session.gc_maxlifetime" set in php.ini).
 * @property bool|null $useCookies The value indicating whether cookies should be used to store session IDs.
 * @property bool $useCustomStorage Whether to use custom storage. This property is read-only.
 * @property bool $useTransparentSessionID Whether transparent sid support is enabled or not, defaults to
 * false.
 *
 * @author Qiang Xue <qiang.xue@gmail.com>
 * @since 2.0
 */
class Session extends Component implements \IteratorAggregate, \ArrayAccess, \Countable
{
    /**
     * @var string the name of the session variable that stores the flash message data.
     */
    public $flashParam = '__flash';
    /**
     * @var \SessionHandlerInterface|array an object implementing the SessionHandlerInterface or a configuration array. If set, will be used to provide persistency instead of build-in methods.
     */
    public $handler;

    /**
     * @var array parameter-value pairs to override default session cookie parameters that are used for session_set_cookie_params() function
     * Array may have the following possible keys: 'lifetime', 'path', 'domain', 'secure', 'httponly'
     * @see https://secure.php.net/manual/en/function.session-set-cookie-params.php
     */
    private $_cookieParams = ['httponly' => true];
    /**
     * @var $frozenSessionData array|null is used for saving session between recreations due to session parameters update.
     */
    private $frozenSessionData;


    /**
     * Initializes the application component.
     * This method is required by IApplicationComponent and is invoked by application.
     */
    public function init()
    {
        parent::init();
        register_shutdown_function([$this, 'close']);
        if ($this->getIsActive()) {
            Yii::warning('Session is already started', __METHOD__);
            $this->updateFlashCounters();
        }
    }

    /**
     * Returns a value indicating whether to use custom session storage.
     * This method should be overridden to return true by child classes that implement custom session storage.
     * To implement custom session storage, override these methods: [[openSession()]], [[closeSession()]],
     * [[readSession()]], [[writeSession()]], [[destroySession()]] and [[gcSession()]].
     * @return bool whether to use custom storage.
     */
    public function getUseCustomStorage()
    {
        return false;
    }

    /**
     * Starts the session.
     */
    public function open()
    {
        if ($this->getIsActive()) {
            return;
        }

        $this->registerSessionHandler();

        $this->setCookieParamsInternal();

        YII_DEBUG ? session_start() : @session_start();

        if ($this->getIsActive()) {
            Yii::info('Session started', __METHOD__);
            $this->updateFlashCounters();
        } else {
            $error = error_get_last();
            $message = isset($error['message']) ? $error['message'] : 'Failed to start session.';
            Yii::error($message, __METHOD__);
        }
    }

    /**
     * Registers session handler.
     * @throws \yii\base\InvalidConfigException
     */
    protected function registerSessionHandler()
    {
        if ($this->handler !== null) {
            if (!is_object($this->handler)) {
                $this->handler = Yii::createObject($this->handler);
            }
            if (!$this->handler instanceof \SessionHandlerInterface) {
                throw new InvalidConfigException('"' . get_class($this) . '::handler" must implement the SessionHandlerInterface.');
            }
            YII_DEBUG ? session_set_save_handler($this->handler, false) : @session_set_save_handler($this->handler, false);
        } elseif ($this->getUseCustomStorage()) {
            if (YII_DEBUG) {
                session_set_save_handler(
                    [$this, 'openSession'],
                    [$this, 'closeSession'],
                    [$this, 'readSession'],
                    [$this, 'writeSession'],
                    [$this, 'destroySession'],
                    [$this, 'gcSession']
                );
            } else {
                @session_set_save_handler(
                    [$this, 'openSession'],
                    [$this, 'closeSession'],
                    [$this, 'readSession'],
                    [$this, 'writeSession'],
                    [$this, 'destroySession'],
                    [$this, 'gcSession']
                );
            }
        }
    }

    /**
     * Ends the current session and store session data.
     */
    public function close()
    {
        if ($this->getIsActive()) {
            YII_DEBUG ? session_write_close() : @session_write_close();
        }
    }

    /**
     * Frees all session variables and destroys all data registered to a session.
     *
     * This method has no effect when session is not [[getIsActive()|active]].
     * Make sure to call [[open()]] before calling it.
     * @see open()
     * @see isActive
     */
    public function destroy()
    {
        if ($this->getIsActive()) {
            $sessionId = session_id();
            $this->close();
            $this->setId($sessionId);
            $this->open();
            session_unset();
            session_destroy();
            $this->setId($sessionId);
        }
    }

    /**
     * @return bool whether the session has started
     */
    public function getIsActive()
    {
        return session_status() === PHP_SESSION_ACTIVE;
    }

    private $_hasSessionId;

    /**
     * Returns a value indicating whether the current request has sent the session ID.
     * The default implementation will check cookie and $_GET using the session name.
     * If you send session ID via other ways, you may need to override this method
     * or call [[setHasSessionId()]] to explicitly set whether the session ID is sent.
     * @return bool whether the current request has sent the session ID.
     */
    public function getHasSessionId()
    {
        if ($this->_hasSessionId === null) {
            $name = $this->getName();
            $request = Yii::$app->getRequest();
            if (!empty($_COOKIE[$name]) && ini_get('session.use_cookies')) {
                $this->_hasSessionId = true;
            } elseif (!ini_get('session.use_only_cookies') && ini_get('session.use_trans_sid')) {
                $this->_hasSessionId = $request->get($name) != '';
            } else {
                $this->_hasSessionId = false;
            }
        }

        return $this->_hasSessionId;
    }

    /**
     * Sets the value indicating whether the current request has sent the session ID.
     * This method is provided so that you can override the default way of determining
     * whether the session ID is sent.
     * @param bool $value whether the current request has sent the session ID.
     */
    public function setHasSessionId($value)
    {
        $this->_hasSessionId = $value;
    }

    /**
     * Gets the session ID.
     * This is a wrapper for [PHP session_id()](https://secure.php.net/manual/en/function.session-id.php).
     * @return string the current session ID
     */
    public function getId()
    {
        return session_id();
    }

    /**
     * Sets the session ID.
     * This is a wrapper for [PHP session_id()](https://secure.php.net/manual/en/function.session-id.php).
     * @param string $value the session ID for the current session
     */
    public function setId($value)
    {
        session_id($value);
    }

    /**
     * Updates the current session ID with a newly generated one.
     *
     * Please refer to <https://secure.php.net/session_regenerate_id> for more details.
     *
     * This method has no effect when session is not [[getIsActive()|active]].
     * Make sure to call [[open()]] before calling it.
     *
     * @param bool $deleteOldSession Whether to delete the old associated session file or not.
     * @see open()
     * @see isActive
     */
    public function regenerateID($deleteOldSession = false)
    {
        if ($this->getIsActive()) {
            // add @ to inhibit possible warning due to race condition
            // https://github.com/yiisoft/yii2/pull/1812
            if (YII_DEBUG && !headers_sent()) {
                session_regenerate_id($deleteOldSession);
            } else {
                @session_regenerate_id($deleteOldSession);
            }
        }
    }

    /**
     * Gets the name of the current session.
     * This is a wrapper for [PHP session_name()](https://secure.php.net/manual/en/function.session-name.php).
     * @return string the current session name
     */
    public function getName()
    {
        return session_name();
    }

    /**
     * Sets the name for the current session.
     * This is a wrapper for [PHP session_name()](https://secure.php.net/manual/en/function.session-name.php).
     * @param string $value the session name for the current session, must be an alphanumeric string.
     * It defaults to "PHPSESSID".
     */
    public function setName($value)
    {
        $this->freeze();
        session_name($value);
        $this->unfreeze();
    }

    /**
     * Gets the current session save path.
     * This is a wrapper for [PHP session_save_path()](https://secure.php.net/manual/en/function.session-save-path.php).
     * @return string the current session save path, defaults to '/tmp'.
     */
    public function getSavePath()
    {
        return session_save_path();
    }

    /**
     * Sets the current session save path.
     * This is a wrapper for [PHP session_save_path()](https://secure.php.net/manual/en/function.session-save-path.php).
     * @param string $value the current session save path. This can be either a directory name or a [path alias](guide:concept-aliases).
     * @throws InvalidArgumentException if the path is not a valid directory
     */
    public function setSavePath($value)
    {
        $path = Yii::getAlias($value);
        if (is_dir($path)) {
            session_save_path($path);
        } else {
            throw new InvalidArgumentException("Session save path is not a valid directory: $value");
        }
    }

    /**
     * @return array the session cookie parameters.
     * @see https://secure.php.net/manual/en/function.session-get-cookie-params.php
     */
    public function getCookieParams()
    {
        return array_merge(session_get_cookie_params(), array_change_key_case($this->_cookieParams));
    }

    /**
     * Sets the session cookie parameters.
     * The cookie parameters passed to this method will be merged with the result
     * of `session_get_cookie_params()`.
     * @param array $value cookie parameters, valid keys include: `lifetime`, `path`, `domain`, `secure` and `httponly`.
     * Starting with Yii 2.0.21 `sameSite` is also supported. It requires PHP version 7.3.0 or higher.
     * For securtiy, an exception will be thrown if `sameSite` is set while using an unsupported version of PHP.
     * To use this feature across different PHP versions check the version first. E.g.
     * ```php
     * [
     *     'sameSite' => PHP_VERSION_ID >= 70300 ? yii\web\Cookie::SAME_SITE_LAX : null,
     * ]
     * ```
     * See https://www.owasp.org/index.php/SameSite for more information about `sameSite`.
     *
     * @throws InvalidArgumentException if the parameters are incomplete.
     * @see https://secure.php.net/manual/en/function.session-set-cookie-params.php
     */
    public function setCookieParams(array $value)
    {
        $this->_cookieParams = $value;
    }

    /**
     * Sets the session cookie parameters.
     * This method is called by [[open()]] when it is about to open the session.
     * @throws InvalidArgumentException if the parameters are incomplete.
     * @see https://secure.php.net/manual/en/function.session-set-cookie-params.php
     */
    private function setCookieParamsInternal()
    {
        $data = $this->getCookieParams();
        if (isset($data['lifetime'], $data['path'], $data['domain'], $data['secure'], $data['httponly'])) {
            if (PHP_VERSION_ID >= 70300) {
                session_set_cookie_params($data);
            } else {
                if (!empty($data['samesite'])) {
                    $data['path'] .= '; samesite=' . $data['samesite'];
                }
                session_set_cookie_params($data['lifetime'], $data['path'], $data['domain'], $data['secure'], $data['httponly']);
            }

        } else {
            throw new InvalidArgumentException('Please make sure cookieParams contains these elements: lifetime, path, domain, secure and httponly.');
        }
    }

    /**
     * Returns the value indicating whether cookies should be used to store session IDs.
     * @return bool|null the value indicating whether cookies should be used to store session IDs.
     * @see setUseCookies()
     */
    public function getUseCookies()
    {
        if (ini_get('session.use_cookies') === '0') {
            return false;
        } elseif (ini_get('session.use_only_cookies') === '1') {
            return true;
        }

        return null;
    }

    /**
     * Sets the value indicating whether cookies should be used to store session IDs.
     *
     * Three states are possible:
     *
     * - true: cookies and only cookies will be used to store session IDs.
     * - false: cookies will not be used to store session IDs.
     * - null: if possible, cookies will be used to store session IDs; if not, other mechanisms will be used (e.g. GET parameter)
     *
     * @param bool|null $value the value indicating whether cookies should be used to store session IDs.
     */
    public function setUseCookies($value)
    {
        $this->freeze();
        if ($value === false) {
            ini_set('session.use_cookies', '0');
            ini_set('session.use_only_cookies', '0');
        } elseif ($value === true) {
            ini_set('session.use_cookies', '1');
            ini_set('session.use_only_cookies', '1');
        } else {
            ini_set('session.use_cookies', '1');
            ini_set('session.use_only_cookies', '0');
        }
        $this->unfreeze();
    }

    /**
     * @return float the probability (percentage) that the GC (garbage collection) process is started on every session initialization.
     */
    public function getGCProbability()
    {
        return (float) (ini_get('session.gc_probability') / ini_get('session.gc_divisor') * 100);
    }

    /**
     * @param float $value the probability (percentage) that the GC (garbage collection) process is started on every session initialization.
     * @throws InvalidArgumentException if the value is not between 0 and 100.
     */
    public function setGCProbability($value)
    {
        $this->freeze();
        if ($value >= 0 && $value <= 100) {
            // percent * 21474837 / 2147483647 ≈ percent * 0.01
            ini_set('session.gc_probability', floor($value * 21474836.47));
            ini_set('session.gc_divisor', 2147483647);
        } else {
            throw new InvalidArgumentException('GCProbability must be a value between 0 and 100.');
        }
        $this->unfreeze();
    }

    /**
     * @return bool whether transparent sid support is enabled or not, defaults to false.
     */
    public function getUseTransparentSessionID()
    {
        return ini_get('session.use_trans_sid') == 1;
    }

    /**
     * @param bool $value whether transparent sid support is enabled or not.
     */
    public function setUseTransparentSessionID($value)
    {
        $this->freeze();
        ini_set('session.use_trans_sid', $value ? '1' : '0');
        $this->unfreeze();
    }

    /**
     * @return int the number of seconds after which data will be seen as 'garbage' and cleaned up.
     * The default value is 1440 seconds (or the value of "session.gc_maxlifetime" set in php.ini).
     */
    public function getTimeout()
    {
        return (int) ini_get('session.gc_maxlifetime');
    }

    /**
     * @param int $value the number of seconds after which data will be seen as 'garbage' and cleaned up
     */
    public function setTimeout($value)
    {
        $this->freeze();
        ini_set('session.gc_maxlifetime', $value);
        $this->unfreeze();
    }

    /**
     * Session open handler.
     * This method should be overridden if [[useCustomStorage]] returns true.
     * @internal Do not call this method directly.
     * @param string $savePath session save path
     * @param string $sessionName session name
     * @return bool whether session is opened successfully
     */
    public function openSession($savePath, $sessionName)
    {
        return true;
    }

    /**
     * Session close handler.
     * This method should be overridden if [[useCustomStorage]] returns true.
     * @internal Do not call this method directly.
     * @return bool whether session is closed successfully
     */
    public function closeSession()
    {
        return true;
    }

    /**
     * Session read handler.
     * This method should be overridden if [[useCustomStorage]] returns true.
     * @internal Do not call this method directly.
     * @param string $id session ID
     * @return string the session data
     */
    public function readSession($id)
    {
        return '';
    }

    /**
     * Session write handler.
     * This method should be overridden if [[useCustomStorage]] returns true.
     * @internal Do not call this method directly.
     * @param string $id session ID
     * @param string $data session data
     * @return bool whether session write is successful
     */
    public function writeSession($id, $data)
    {
        return true;
    }

    /**
     * Session destroy handler.
     * This method should be overridden if [[useCustomStorage]] returns true.
     * @internal Do not call this method directly.
     * @param string $id session ID
     * @return bool whether session is destroyed successfully
     */
    public function destroySession($id)
    {
        return true;
    }

    /**
     * Session GC (garbage collection) handler.
     * This method should be overridden if [[useCustomStorage]] returns true.
     * @internal Do not call this method directly.
     * @param int $maxLifetime the number of seconds after which data will be seen as 'garbage' and cleaned up.
     * @return bool whether session is GCed successfully
     */
    public function gcSession($maxLifetime)
    {
        return true;
    }

    /**
     * Returns an iterator for traversing the session variables.
     * This method is required by the interface [[\IteratorAggregate]].
     * @return SessionIterator an iterator for traversing the session variables.
     */
    public function getIterator()
    {
        $this->open();
        return new SessionIterator();
    }

    /**
     * Returns the number of items in the session.
     * @return int the number of session variables
     */
    public function getCount()
    {
        $this->open();
        return count($_SESSION);
    }

    /**
     * Returns the number of items in the session.
     * This method is required by [[\Countable]] interface.
     * @return int number of items in the session.
     */
    public function count()
    {
        return $this->getCount();
    }

    /**
     * Returns the session variable value with the session variable name.
     * If the session variable does not exist, the `$defaultValue` will be returned.
     * @param string $key the session variable name
     * @param mixed $defaultValue the default value to be returned when the session variable does not exist.
     * @return mixed the session variable value, or $defaultValue if the session variable does not exist.
     */
    public function get($key, $defaultValue = null)
    {
        $this->open();
        return isset($_SESSION[$key]) ? $_SESSION[$key] : $defaultValue;
    }

    /**
     * Adds a session variable.
     * If the specified name already exists, the old value will be overwritten.
     * @param string $key session variable name
     * @param mixed $value session variable value
     */
    public function set($key, $value)
    {
        $this->open();
        $_SESSION[$key] = $value;
    }

    /**
     * Removes a session variable.
     * @param string $key the name of the session variable to be removed
     * @return mixed the removed value, null if no such session variable.
     */
    public function remove($key)
    {
        $this->open();
        if (isset($_SESSION[$key])) {
            $value = $_SESSION[$key];
            unset($_SESSION[$key]);

            return $value;
        }

        return null;
    }

    /**
     * Removes all session variables.
     */
    public function removeAll()
    {
        $this->open();
        foreach (array_keys($_SESSION) as $key) {
            unset($_SESSION[$key]);
        }
    }

    /**
     * @param mixed $key session variable name
     * @return bool whether there is the named session variable
     */
    public function has($key)
    {
        $this->open();
        return isset($_SESSION[$key]);
    }

    /**
     * Updates the counters for flash messages and removes outdated flash messages.
     * This method should only be called once in [[init()]].
     */
    protected function updateFlashCounters()
    {
        $counters = $this->get($this->flashParam, []);
        if (is_array($counters)) {
            foreach ($counters as $key => $count) {
                if ($count > 0) {
                    unset($counters[$key], $_SESSION[$key]);
                } elseif ($count == 0) {
                    $counters[$key]++;
                }
            }
            $_SESSION[$this->flashParam] = $counters;
        } else {
            // fix the unexpected problem that flashParam doesn't return an array
            unset($_SESSION[$this->flashParam]);
        }
    }

    /**
     * Returns a flash message.
     * @param string $key the key identifying the flash message
     * @param mixed $defaultValue value to be returned if the flash message does not exist.
     * @param bool $delete whether to delete this flash message right after this method is called.
     * If false, the flash message will be automatically deleted in the next request.
     * @return mixed the flash message or an array of messages if addFlash was used
     * @see setFlash()
     * @see addFlash()
     * @see hasFlash()
     * @see getAllFlashes()
     * @see removeFlash()
     */
    public function getFlash($key, $defaultValue = null, $delete = false)
    {
        $counters = $this->get($this->flashParam, []);
        if (isset($counters[$key])) {
            $value = $this->get($key, $defaultValue);
            if ($delete) {
                $this->removeFlash($key);
            } elseif ($counters[$key] < 0) {
                // mark for deletion in the next request
                $counters[$key] = 1;
                $_SESSION[$this->flashParam] = $counters;
            }

            return $value;
        }

        return $defaultValue;
    }

    /**
     * Returns all flash messages.
     *
     * You may use this method to display all the flash messages in a view file:
     *
     * ```php
     * <?php
     * foreach (Yii::$app->session->getAllFlashes() as $key => $message) {
     *     echo '<div class="alert alert-' . $key . '">' . $message . '</div>';
     * } ?>
     * ```
     *
     * With the above code you can use the [bootstrap alert][] classes such as `success`, `info`, `danger`
     * as the flash message key to influence the color of the div.
     *
     * Note that if you use [[addFlash()]], `$message` will be an array, and you will have to adjust the above code.
     *
     * [bootstrap alert]: http://getbootstrap.com/components/#alerts
     *
     * @param bool $delete whether to delete the flash messages right after this method is called.
     * If false, the flash messages will be automatically deleted in the next request.
     * @return array flash messages (key => message or key => [message1, message2]).
     * @see setFlash()
     * @see addFlash()
     * @see getFlash()
     * @see hasFlash()
     * @see removeFlash()
     */
    public function getAllFlashes($delete = false)
    {
        $counters = $this->get($this->flashParam, []);
        $flashes = [];
        foreach (array_keys($counters) as $key) {
            if (array_key_exists($key, $_SESSION)) {
                $flashes[$key] = $_SESSION[$key];
                if ($delete) {
                    unset($counters[$key], $_SESSION[$key]);
                } elseif ($counters[$key] < 0) {
                    // mark for deletion in the next request
                    $counters[$key] = 1;
                }
            } else {
                unset($counters[$key]);
            }
        }

        $_SESSION[$this->flashParam] = $counters;

        return $flashes;
    }

    /**
     * Sets a flash message.
     * A flash message will be automatically deleted after it is accessed in a request and the deletion will happen
     * in the next request.
     * If there is already an existing flash message with the same key, it will be overwritten by the new one.
     * @param string $key the key identifying the flash message. Note that flash messages
     * and normal session variables share the same name space. If you have a normal
     * session variable using the same name, its value will be overwritten by this method.
     * @param mixed $value flash message
     * @param bool $removeAfterAccess whether the flash message should be automatically removed only if
     * it is accessed. If false, the flash message will be automatically removed after the next request,
     * regardless if it is accessed or not. If true (default value), the flash message will remain until after
     * it is accessed.
     * @see getFlash()
     * @see addFlash()
     * @see removeFlash()
     */
    public function setFlash($key, $value = true, $removeAfterAccess = true)
    {
        $counters = $this->get($this->flashParam, []);
        $counters[$key] = $removeAfterAccess ? -1 : 0;
        $_SESSION[$key] = $value;
        $_SESSION[$this->flashParam] = $counters;
    }

    /**
     * Adds a flash message.
     * If there are existing flash messages with the same key, the new one will be appended to the existing message array.
     * @param string $key the key identifying the flash message.
     * @param mixed $value flash message
     * @param bool $removeAfterAccess whether the flash message should be automatically removed only if
     * it is accessed. If false, the flash message will be automatically removed after the next request,
     * regardless if it is accessed or not. If true (default value), the flash message will remain until after
     * it is accessed.
     * @see getFlash()
     * @see setFlash()
     * @see removeFlash()
     */
    public function addFlash($key, $value = true, $removeAfterAccess = true)
    {
        $counters = $this->get($this->flashParam, []);
        $counters[$key] = $removeAfterAccess ? -1 : 0;
        $_SESSION[$this->flashParam] = $counters;
        if (empty($_SESSION[$key])) {
            $_SESSION[$key] = [$value];
        } elseif (is_array($_SESSION[$key])) {
            $_SESSION[$key][] = $value;
        } else {
            $_SESSION[$key] = [$_SESSION[$key], $value];
        }
    }

    /**
     * Removes a flash message.
     * @param string $key the key identifying the flash message. Note that flash messages
     * and normal session variables share the same name space.  If you have a normal
     * session variable using the same name, it will be removed by this method.
     * @return mixed the removed flash message. Null if the flash message does not exist.
     * @see getFlash()
     * @see setFlash()
     * @see addFlash()
     * @see removeAllFlashes()
     */
    public function removeFlash($key)
    {
        $counters = $this->get($this->flashParam, []);
        $value = isset($_SESSION[$key], $counters[$key]) ? $_SESSION[$key] : null;
        unset($counters[$key], $_SESSION[$key]);
        $_SESSION[$this->flashParam] = $counters;

        return $value;
    }

    /**
     * Removes all flash messages.
     * Note that flash messages and normal session variables share the same name space.
     * If you have a normal session variable using the same name, it will be removed
     * by this method.
     * @see getFlash()
     * @see setFlash()
     * @see addFlash()
     * @see removeFlash()
     */
    public function removeAllFlashes()
    {
        $counters = $this->get($this->flashParam, []);
        foreach (array_keys($counters) as $key) {
            unset($_SESSION[$key]);
        }
        unset($_SESSION[$this->flashParam]);
    }

    /**
     * Returns a value indicating whether there are flash messages associated with the specified key.
     * @param string $key key identifying the flash message type
     * @return bool whether any flash messages exist under specified key
     */
    public function hasFlash($key)
    {
        return $this->getFlash($key) !== null;
    }

    /**
     * This method is required by the interface [[\ArrayAccess]].
     * @param mixed $offset the offset to check on
     * @return bool
     */
    public function offsetExists($offset)
    {
        $this->open();

        return isset($_SESSION[$offset]);
    }

    /**
     * This method is required by the interface [[\ArrayAccess]].
     * @param int $offset the offset to retrieve element.
     * @return mixed the element at the offset, null if no element is found at the offset
     */
    public function offsetGet($offset)
    {
        $this->open();

        return isset($_SESSION[$offset]) ? $_SESSION[$offset] : null;
    }

    /**
     * This method is required by the interface [[\ArrayAccess]].
     * @param int $offset the offset to set element
     * @param mixed $item the element value
     */
    public function offsetSet($offset, $item)
    {
        $this->open();
        $_SESSION[$offset] = $item;
    }

    /**
     * This method is required by the interface [[\ArrayAccess]].
     * @param mixed $offset the offset to unset element
     */
    public function offsetUnset($offset)
    {
        $this->open();
        unset($_SESSION[$offset]);
    }

    /**
     * If session is started it's not possible to edit session ini settings. In PHP7.2+ it throws exception.
     * This function saves session data to temporary variable and stop session.
     * @since 2.0.14
     */
    protected function freeze()
    {
        if ($this->getIsActive()) {
            if (isset($_SESSION)) {
                $this->frozenSessionData = $_SESSION;
            }
            $this->close();
            Yii::info('Session frozen', __METHOD__);
        }
    }

    /**
     * Starts session and restores data from temporary variable
     * @since 2.0.14
     */
    protected function unfreeze()
    {
        if (null !== $this->frozenSessionData) {

            YII_DEBUG ? session_start() : @session_start();

            if ($this->getIsActive()) {
                Yii::info('Session unfrozen', __METHOD__);
            } else {
                $error = error_get_last();
                $message = isset($error['message']) ? $error['message'] : 'Failed to unfreeze session.';
                Yii::error($message, __METHOD__);
            }

            $_SESSION = $this->frozenSessionData;
            $this->frozenSessionData = null;
        }
    }

    /**
     * Set cache limiter
     *
     * @param string $cacheLimiter
     * @since 2.0.14
     */
    public function setCacheLimiter($cacheLimiter)
    {
        $this->freeze();
        session_cache_limiter($cacheLimiter);
        $this->unfreeze();
    }

    /**
     * Returns current cache limiter
     *
     * @return string current cache limiter
     * @since 2.0.14
     */
    public function getCacheLimiter()
    {
        return session_cache_limiter();
    }
}

```


<br/><br/><br/><br/><br/>
## 参考资料

<https://www.yiichina.com/doc/guide/2.0/runtime-sessions-cookies>

<https://www.yiichina.com/tutorial/840>

应用组件 <https://www.yiichina.com/doc/guide/2.0/structure-application-components>

服务定位器（Service Locator） <https://www.yiichina.com/doc/guide/2.0/concept-service-locator>