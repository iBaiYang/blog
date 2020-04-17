---
layout: post
categories: Yii2
title: Yii2 详说auth_key的用途
meta: Yii2 详说auth_key的用途
---
* content
{:toc}

### 正文

<http://blog.sina.com.cn/s/blog_6aba78b40102x6vw.html>

当我们用yii2开始项目时，在migrate初始生成user表时，会看到里面有一个auth_key字段，这个字段是做什么用的呢？查阅好多资料后，还是云里雾里，莫名其妙，不知所从。其实这个字段是用来用cookie自动登录验证的，这样即使session丢失了，只要cookie信息还存在，那么就可以用cookie中的相关信息完成自动登录并写入session。

这里有详细的解说过程，可以看看：<http://www.yii-china.com/post/detail/323.html>

#### 引入对比
 
我们看看cookie中发生了什么，后台login登录时，用F12可以查看：

![](https://raw.githubusercontent.com/iBaiYang/PictureWareroom/master/20200417/20200417132763.jpeg)

_csrf-backend详细： 
```
v5G4eIFl_Lel0yPkbWPqDw1l7jJWYknp9AXY6sF_xKeLwOIgyVXL1dyBELteFKtVTwqoAw8hFr-VSo2c9x2vyg==
```

关于csrf可以看这里：<https://ibaiyang.github.io/blog/yii2/2017/12/03/Yii2-csrf%E7%BB%BC%E5%90%88.html>

![](https://raw.githubusercontent.com/iBaiYang/PictureWareroom/master/20200417/20200417132764.jpeg)

请求Cookie中：

_csrf-backend详细： 
```

```
e464895e073b220c0265aa9148836f833eedefd2cafb61cbfa24fbf940d71967a:2:{i:0;s:13:"_csrf-backend";i:1;s:32:"4QZXH07byR3_3wAZBoF1YC_VaOUv6bkm";}

响应Cookie中：

_identity-backend的value详细：
```

```
bb2e3c9536c3fe6a1399519c4d43c3e7a0565fdeed79f33d95efbad9e1486781a:2:{i:0;s:17:"_identity-backend";i:1;s:46:"[1,"ZvB1Jn0NvP9M0vdLt82W9zVs8ROo8NSS",2592000]";}

这里既有advanced-backend、又有_csrf-backend、还有_identity-backend，是由于在配置文件backend/config/main.php中有：

```
'components' => [
    'request' => [
        'csrfParam' => '_csrf-backend',
    ],
    'user' => [
        'identityClass' => 'common\models\Adminuser',
        'enableAutoLogin' => true,
        'identityCookie' => ['name' => '_identity-backend', 'httpOnly' => true],
    ],
    'session' => [
        // this is the name of the session cookie used for login on the backend
        'name' => 'advanced-backend',
//        'savePath' => sys_get_temp_dir(),
    ],
],   
```

advanced-backend是session中配置的，可以换个名字，如：session-backend。
用户用浏览器第一次访问网站，网站服务器会给用户浏览器标记一个session，就是这时候给的advanced-backend值，
20分钟内不会再发这个性质的session了。

_csrf-backend是request中配置的，用户用浏览器第一次访问网站域名时，并不会传递过来，
第一次访问网站页面（Yii2框架配置的）时，才会传递过来。以后访问该网站的页面，都会带上这个cookie参数，
网站也不再传递这个值过来。

_identity-backend是user组件中配置的，正如其名，是用户post登陆成功后传递过来的。在在线情况下，
每次都会传递这个值过来以更新有效期。退出时又传递这个值过来，不过这个值为空。

传递过来的cookie值：value是值的内容；path是存在浏览器上的位置，
httpOnly:true说明只有http请求才会传递这个值到服务器，expires是这个cookie存到什么时间截至。

cookie就是服务器辨识浏览器的标志，浏览器每一次访问服务器，都会带上这个服务器存在这个浏览器上的所有cookie信息。

登录后，访问页面admin-user/index时：

![](https://raw.githubusercontent.com/iBaiYang/PictureWareroom/master/20200417/20200417132765.jpeg)

响应Cookie中：

_identity-backend的value详细：
```
bb2e3c9536c3fe6a1399519c4d43c3e7a0565fdeed79f33d95efbad9e1486781a:2:{i:0;s:17:"_identity-backend";i:1;s:46:"[1,"ZvB1Jn0NvP9M0vdLt82W9zVs8ROo8NSS",2592000]";}
```

请求Cookie中：

_identity-backend详细： 
```
bb2e3c9536c3fe6a1399519c4d43c3e7a0565fdeed79f33d95efbad9e1486781a:2:{i:0;s:17:"_identity-backend";i:1;s:46:"[1,"ZvB1Jn0NvP9M0vdLt82W9zVs8ROo8NSS",2592000]";}
```

_csrf-backend详细：
```
e464895e073b220c0265aa9148836f833eedefd2cafb61cbfa24fbf940d71967a:2:{i:0;s:13:"_csrf-backend";i:1;s:32:"4QZXH07byR3_3wAZBoF1YC_VaOUv6bkm";}
```

退出时：

![](https://raw.githubusercontent.com/iBaiYang/PictureWareroom/master/20200417/20200417132766.jpeg)

_csrf-backend详细：
```
CuwhawBszNhDMPHaMKuybDNL1spYBVZl0jTvsCdMB9M-vXszSFz7ujpiwoUD3PM2cSSQ-wFGCTOze7rGES5svg==
```

![](https://raw.githubusercontent.com/iBaiYang/PictureWareroom/master/20200417/20200417132767.jpeg)

请求Cookie中：

_identity-backend详细： 
```
bb2e3c9536c3fe6a1399519c4d43c3e7a0565fdeed79f33d95efbad9e1486781a:2:{i:0;s:17:"_identity-backend";i:1;s:46:"[1,"ZvB1Jn0NvP9M0vdLt82W9zVs8ROo8NSS",2592000]";}
```

_csrf-backend详细：
```
e464895e073b220c0265aa9148836f833eedefd2cafb61cbfa24fbf940d71967a:2:{i:0;s:13:"_csrf-backend";i:1;s:32:"4QZXH07byR3_3wAZBoF1YC_VaOUv6bkm";}
```

响应Cookie中：

_identity-backend做删除（deleted）操作。

退出后，访问页面site/index时：

![](https://raw.githubusercontent.com/iBaiYang/PictureWareroom/master/20200417/20200417132768.jpeg)

_csrf-backend详细：
```
e464895e073b220c0265aa9148836f833eedefd2cafb61cbfa24fbf940d71967a:2:{i:0;s:13:"_csrf-backend";i:1;s:32:"4QZXH07byR3_3wAZBoF1YC_VaOUv6bkm";}
```

可以看到没有_identity-backend。

紧接着再次登录：

![](https://raw.githubusercontent.com/iBaiYang/PictureWareroom/master/20200417/20200417132769.jpeg)

![](https://raw.githubusercontent.com/iBaiYang/PictureWareroom/master/20200417/20200417132770.jpeg)

注意对比上面的异同。

#### 过程研究

接下来，我们研究下过程。

backend/controllers/SiteController中：

```
    /**
     * Login action.
     *
     * @return string
     */
    public function actionLogin()
    {
        if (!Yii::$app->user->isGuest) {
            return $this->goHome();
        }

        $model = new AdminLoginForm();
        if ($model->load(Yii::$app->request->post()) && $model->login()) {
            return $this->goBack();
        } else {
            return $this->render('login', [
                'model' => $model,
            ]);
        }
    }
```

我们继承yii\base\Model并改造的common/models/AdminLoginForm中：

```
    /**
     * Logs in a user using the provided username and password.
     *
     * @return bool whether the user is logged in successfully
     */
    public function login()
    {
        if ($this->validate()) {
            return Yii::$app->user->login($this->getUser(), $this->rememberMe ? 3600 * 24 * 30 : 0);
        } else {
            return false;
        }
    }
```

这里user组件：

```
'components' => [
    'user' => [
        'identityClass' => 'common\models\Adminuser',
        'enableAutoLogin' => true,
        'identityCookie' => ['name' => '_identity-backend', 'httpOnly' => true],
    ],
],   
```

common\models\Adminuser继承\yii\db\ActiveRecord，实现yii\web\IdentityInterface：

```
class Adminuser extends \yii\db\ActiveRecord implements \yii\web\IdentityInterface
{
    ...
}
```

在这里你是找不到login方法的。上面的配置应该说只是配置组件的参数，真正的加载组件要从下面来寻找：

backend\web\index.php:

```
defined('YII_DEBUG') or define('YII_DEBUG', true);
defined('YII_ENV') or define('YII_ENV', 'dev');

require(__DIR__ . '/../../vendor/autoload.php');
require(__DIR__ . '/../../vendor/yiisoft/yii2/Yii.php');
require(__DIR__ . '/../../common/config/bootstrap.php');
require(__DIR__ . '/../config/bootstrap.php');

$config = yii\helpers\ArrayHelper::merge(
    require(__DIR__ . '/../../common/config/main.php'),
    require(__DIR__ . '/../../common/config/main-local.php'),
    require(__DIR__ . '/../config/main.php'),
    require(__DIR__ . '/../config/main-local.php')
);

(new yii\web\Application($config))->run();
```

yii\web\Application中：

```
    /**
     * Returns the user component.
     * @return User the user component.
     */
    public function getUser()
    {
        return $this->get('user');
    }

    /**
     * @inheritdoc
     */
    public function coreComponents()
    {
        return array_merge(parent::coreComponents(), [
            'request' => ['class' => 'yii\web\Request'],
            'response' => ['class' => 'yii\web\Response'],
            'session' => ['class' => 'yii\web\Session'],
            'user' => ['class' => 'yii\web\User'],
            'errorHandler' => ['class' => 'yii\web\ErrorHandler'],
        ]);
    }
```

yii\web\User中：

```
    /**
     * Logs in a user.
     *
     * After logging in a user:
     * - the user's identity information is obtainable from the [[identity]] property
     *
     * If [[enableSession]] is `true`:
     * - the identity information will be stored in session and be available in the next requests
     * - in case of `$duration == 0`: as long as the session remains active or till the user closes the browser
     * - in case of `$duration > 0`: as long as the session remains active or as long as the cookie
     *   remains valid by it's `$duration` in seconds when [[enableAutoLogin]] is set `true`.
     *
     * If [[enableSession]] is `false`:
     * - the `$duration` parameter will be ignored
     *
     * @param IdentityInterface $identity the user identity (which should already be authenticated)
     * @param int $duration number of seconds that the user can remain in logged-in status, defaults to `0`
     * @return bool whether the user is logged in
     */
    public function login(IdentityInterface $identity, $duration = 0)
    {
        if ($this->beforeLogin($identity, false, $duration)) {
            $this->switchIdentity($identity, $duration);
            $id = $identity->getId();
            $ip = Yii::$app->getRequest()->getUserIP();
            if ($this->enableSession) {
                $log = "User '$id' logged in from $ip with duration $duration.";
            } else {
                $log = "User '$id' logged in from $ip. Session not enabled.";
            }
            Yii::info($log, __METHOD__);
            $this->afterLogin($identity, false, $duration);
        }

        return !$this->getIsGuest();
    }

    /**
     * Switches to a new identity for the current user.
     *
     * When [[enableSession]] is true, this method may use session and/or cookie to store the user identity information,
     * according to the value of `$duration`. Please refer to [[login()]] for more details.
     *
     * This method is mainly called by [[login()]], [[logout()]] and [[loginByCookie()]]
     * when the current user needs to be associated with the corresponding identity information.
     *
     * @param IdentityInterface|null $identity the identity information to be associated with the current user.
     * If null, it means switching the current user to be a guest.
     * @param int $duration number of seconds that the user can remain in logged-in status.
     * This parameter is used only when `$identity` is not null.
     */
    public function switchIdentity($identity, $duration = 0)
    {
        $this->setIdentity($identity);

        if (!$this->enableSession) {
            return;
        }

        /* Ensure any existing identity cookies are removed. */
        if ($this->enableAutoLogin) {
            $this->removeIdentityCookie();
        }

        $session = Yii::$app->getSession();
        if (!YII_ENV_TEST) {
            $session->regenerateID(true);
        }
        $session->remove($this->idParam);
        $session->remove($this->authTimeoutParam);

        if ($identity) {
            $session->set($this->idParam, $identity->getId());
            if ($this->authTimeout !== null) {
                $session->set($this->authTimeoutParam, time() + $this->authTimeout);
            }
            if ($this->absoluteAuthTimeout !== null) {
                $session->set($this->absoluteAuthTimeoutParam, time() + $this->absoluteAuthTimeout);
            }
            if ($duration > 0 && $this->enableAutoLogin) {
                $this->sendIdentityCookie($identity, $duration);
            }
        }
    }

    /**
     * Sends an identity cookie.
     * This method is used when [[enableAutoLogin]] is true.
     * It saves [[id]], [[IdentityInterface::getAuthKey()|auth key]], and the duration of cookie-based login
     * information in the cookie.
     * @param IdentityInterface $identity
     * @param int $duration number of seconds that the user can remain in logged-in status.
     * @see loginByCookie()
     */
    protected function sendIdentityCookie($identity, $duration)
    {
        $cookie = new Cookie($this->identityCookie);
        $cookie->value = json_encode([
            $identity->getId(),
            $identity->getAuthKey(),
            $duration,
        ], JSON_UNESCAPED_SLASHES | JSON_UNESCAPED_UNICODE);
        $cookie->expire = time() + $duration;
        Yii::$app->getResponse()->getCookies()->add($cookie);
    }
```

switchIdentity设置认证信息，登录说到底就是在设置认证信息。

这个方法主要有三个功能

1.设置session的有效期

2.如果cookie的有效期大于0并且允许自动登录，那么就把用户的认证信息保存到cookie中

3.如果允许自动登录，删除cookie信息。这个是用于退出的时候调用的。退出的时候传递进来的$identity为null

sendIdentityCookie存储在cookie中的用户信息包含有三个值：

$identity->getId()

$identity->getAuthKey()

$duration

getId()和getAuthKey()是在IdentityInterface接口中的。我们也知道在设置User组件的时候，
这个User Model是必须要实现IdentityInterface接口的。所以，可以在User Model中得到前两个值，
第三值就是cookie的有效期。


common\models\Adminuser继承\yii\db\ActiveRecord，实现yii\web\IdentityInterface：

```
class Adminuser extends \yii\db\ActiveRecord implements \yii\web\IdentityInterface
{
    ...

    /**
     * @inheritdoc
     */
    public function getId()
    {
        return $this->getPrimaryKey();
    }

    /**
     * @inheritdoc
     */
    public function getAuthKey()
    {
        return $this->auth_key;
    }

    /**
     * @inheritdoc
     */
    public function validateAuthKey($authKey)
    {
        return $this->getAuthKey() === $authKey;
    }

    /**
     * Validates password
     *
     * @param string $password password to validate
     * @return bool if password provided is valid for current user
     */
    public function validatePassword($password)
    {
        return Yii::$app->security->validatePassword($password, $this->password_hash);
    }

    /**
     * Generates password hash from password and sets it to the model
     *
     * @param string $password
     */
    public function setPassword($password)
    {
        $this->password_hash = Yii::$app->security->generatePasswordHash($password);
    }

    /**
     * Generates "remember me" authentication key
     */
    public function generateAuthKey()
    {
        $this->auth_key = Yii::$app->security->generateRandomString();
    }

    /**
     * Generates new password reset token
     */
    public function generatePasswordResetToken()
    {
        $this->password_reset_token = Yii::$app->security->generateRandomString() . '_' . time();
    }

    /**
     * Removes password reset token
     */
    public function removePasswordResetToken()
    {
        $this->password_reset_token = null;
    }    
}
```

#### cookie登录

从上面我们知道用户的认证信息已经存储到cookie中了，那么下次的时候直接从cookie里面取信息然后设置就可以了。

Yii提供了AccessControl来判断用户是否登录，有了这个就不需要在每一个action里面再判断了。

我们看一下backend\controllers\SiteController中：

```
use yii\filters\AccessControl;

...

    /**
     * @inheritdoc
     */
    public function behaviors()
    {
        return [
            'access' => [
                'class' => AccessControl::className(),
                'rules' => [
                    [
                        'actions' => ['login', 'error'],
                        'allow' => true,
                    ],
                    [
                        'actions' => ['logout', 'index'],
                        'allow' => true,
                        'roles' => ['@'],
                    ],
                ],
            ],
            'verbs' => [
                'class' => VerbFilter::className(),
                'actions' => [
                    'logout' => ['post'],
                ],
            ],
        ];
    }
```

yii\filters\AccessControl中：

```
namespace yii\filters;

use Yii;
use yii\base\Action;
use yii\base\ActionFilter;
use yii\di\Instance;
use yii\web\User;
use yii\web\ForbiddenHttpException;

/**
 * AccessControl provides simple access control based on a set of rules.
 *
 * AccessControl is an action filter. It will check its [[rules]] to find
 * the first rule that matches the current context variables (such as user IP address, user role).
 * The matching rule will dictate whether to allow or deny the access to the requested controller
 * action. If no rule matches, the access will be denied.
 *
 * To use AccessControl, declare it in the `behaviors()` method of your controller class.
 * For example, the following declarations will allow authenticated users to access the "create"
 * and "update" actions and deny all other users from accessing these two actions.
 *
 *
 * public function behaviors()
 * {
 *     return [
 *         'access' => [
 *             'class' => \yii\filters\AccessControl::className(),
 *             'only' => ['create', 'update'],
 *             'rules' => [
 *                 // deny all POST requests
 *                 [
 *                     'allow' => false,
 *                     'verbs' => ['POST']
 *                 ],
 *                 // allow authenticated users
 *                 [
 *                     'allow' => true,
 *                     'roles' => ['@'],
 *                 ],
 *                 // everything else is denied
 *             ],
 *         ],
 *     ];
 * }
 * 
 *
 * @author Qiang Xue <qiang.xue@gmail.com>
 * @since 2.0
 */
class AccessControl extends ActionFilter
{
    /**
     * @var User|array|string|false the user object representing the authentication status or the ID of the user application component.
     * Starting from version 2.0.2, this can also be a configuration array for creating the object.
     * Starting from version 2.0.12, you can set it to `false` to explicitly switch this component support off for the filter.
     */
    public $user = 'user';
    /**
     * @var callable a callback that will be called if the access should be denied
     * to the current user. If not set, [[denyAccess()]] will be called.
     *
     * The signature of the callback should be as follows:
     *
     * 
     * function ($rule, $action)
     * 
     *
     * where `$rule` is the rule that denies the user, and `$action` is the current [[Action|action]] object.
     * `$rule` can be `null` if access is denied because none of the rules matched.
     */
    public $denyCallback;
    /**
     * @var array the default configuration of access rules. Individual rule configurations
     * specified via [[rules]] will take precedence when the same property of the rule is configured.
     */
    public $ruleConfig = ['class' => 'yii\filters\AccessRule'];
    /**
     * @var array a list of access rule objects or configuration arrays for creating the rule objects.
     * If a rule is specified via a configuration array, it will be merged with [[ruleConfig]] first
     * before it is used for creating the rule object.
     * @see ruleConfig
     */
    public $rules = [];


    /**
     * Initializes the [[rules]] array by instantiating rule objects from configurations.
     */
    public function init()
    {
        parent::init();
        if ($this->user !== false) {
            $this->user = Instance::ensure($this->user, User::className());
        }
        foreach ($this->rules as $i => $rule) {
            if (is_array($rule)) {
                $this->rules[$i] = Yii::createObject(array_merge($this->ruleConfig, $rule));
            }
        }
    }

    /**
     * This method is invoked right before an action is to be executed (after all possible filters.)
     * You may override this method to do last-minute preparation for the action.
     * @param Action $action the action to be executed.
     * @return bool whether the action should continue to be executed.
     */
    public function beforeAction($action)
    {
        $user = $this->user;
        $request = Yii::$app->getRequest();
        /* @var $rule AccessRule */
        foreach ($this->rules as $rule) {
            if ($allow = $rule->allows($action, $user, $request)) {
                return true;
            } elseif ($allow === false) {
                if (isset($rule->denyCallback)) {
                    call_user_func($rule->denyCallback, $rule, $action);
                } elseif ($this->denyCallback !== null) {
                    call_user_func($this->denyCallback, $rule, $action);
                } else {
                    $this->denyAccess($user);
                }
                return false;
            }
        }
        if ($this->denyCallback !== null) {
            call_user_func($this->denyCallback, null, $action);
        } else {
            $this->denyAccess($user);
        }
        return false;
    }

    /**
     * Denies the access of the user.
     * The default implementation will redirect the user to the login page if he is a guest;
     * if the user is already logged, a 403 HTTP exception will be thrown.
     * @param User|false $user the current user or boolean `false` in case of detached User component
     * @throws ForbiddenHttpException if the user is already logged in or in case of detached User component.
     */
    protected function denyAccess($user)
    {
        if ($user !== false && $user->getIsGuest()) {
            $user->loginRequired();
        } else {
            throw new ForbiddenHttpException(Yii::t('yii', 'You are not allowed to perform this action.'));
        }
    }
}
```

yii\filters\AccessRule中：

```
    /**
     * Checks whether the Web user is allowed to perform the specified action.
     * @param Action $action the action to be performed
     * @param User|false $user the user object or `false` in case of detached User component
     * @param Request $request
     * @return bool|null `true` if the user is allowed, `false` if the user is denied, `null` if the rule does not apply to the user
     */
    public function allows($action, $user, $request)
    {
        if ($this->matchAction($action)
            && $this->matchRole($user)
            && $this->matchIP($request->getUserIP())
            && $this->matchVerb($request->getMethod())
            && $this->matchController($action->controller)
            && $this->matchCustom($action)
        ) {
            return $this->allow ? true : false;
        }

        return null;
    }

    /**
     * @param User $user the user object
     * @return bool whether the rule applies to the role
     * @throws InvalidConfigException if User component is detached
     */
    protected function matchRole($user)
    {
        if (empty($this->roles)) {
            return true;
        }
        if ($user === false) {
            throw new InvalidConfigException('The user application component must be available to specify roles in AccessRule.');
        }
        foreach ($this->roles as $role) {
            if ($role === '?') {
                if ($user->getIsGuest()) {
                    return true;
                }
            } elseif ($role === '@') {
                if (!$user->getIsGuest()) {
                    return true;
                }
            } else {
                if (!isset($roleParams)) {
                    $roleParams = $this->roleParams instanceof Closure ? call_user_func($this->roleParams, $this) : $this->roleParams;
                }
                if ($user->can($role, $roleParams)) {
                    return true;
                }
            }
        }

        return false;
    }
```

yii\web\User中：

```
    /**
     * Returns a value indicating whether the user is a guest (not authenticated).
     * @return bool whether the current user is a guest.
     * @see getIdentity()
     */
    public function getIsGuest()
    {
        return $this->getIdentity() === null;
    }

    /**
     * Returns the identity object associated with the currently logged-in user.
     * When [[enableSession]] is true, this method may attempt to read the user's authentication data
     * stored in session and reconstruct the corresponding identity object, if it has not done so before.
     * @param bool $autoRenew whether to automatically renew authentication status if it has not been done so before.
     * This is only useful when [[enableSession]] is true.
     * @return IdentityInterface|null the identity object associated with the currently logged-in user.
     * `null` is returned if the user is not logged in (not authenticated).
     * @see login()
     * @see logout()
     */
    public function getIdentity($autoRenew = true)
    {
        if ($this->_identity === false) {
            if ($this->enableSession && $autoRenew) {
                $this->_identity = null;
                $this->renewAuthStatus();
            } else {
                return null;
            }
        }

        return $this->_identity;
    }

    /**
     * Updates the authentication status using the information from session and cookie.
     *
     * This method will try to determine the user identity using the [[idParam]] session variable.
     *
     * If [[authTimeout]] is set, this method will refresh the timer.
     *
     * If the user identity cannot be determined by session, this method will try to [[loginByCookie()|login by cookie]]
     * if [[enableAutoLogin]] is true.
     */
    protected function renewAuthStatus()
    {
        $session = Yii::$app->getSession();
        $id = $session->getHasSessionId() || $session->getIsActive() ? $session->get($this->idParam) : null;

        if ($id === null) {
            $identity = null;
        } else {
            /* @var $class IdentityInterface */
            $class = $this->identityClass;
            $identity = $class::findIdentity($id);
        }

        $this->setIdentity($identity);

        if ($identity !== null && ($this->authTimeout !== null || $this->absoluteAuthTimeout !== null)) {
            $expire = $this->authTimeout !== null ? $session->get($this->authTimeoutParam) : null;
            $expireAbsolute = $this->absoluteAuthTimeout !== null ? $session->get($this->absoluteAuthTimeoutParam) : null;
            if ($expire !== null && $expire < time() || $expireAbsolute !== null && $expireAbsolute < time()) {
                $this->logout(false);
            } elseif ($this->authTimeout !== null) {
                $session->set($this->authTimeoutParam, time() + $this->authTimeout);
            }
        }

        if ($this->enableAutoLogin) {
            if ($this->getIsGuest()) {
                $this->loginByCookie();
            } elseif ($this->autoRenewCookie) {
                $this->renewIdentityCookie();
            }
        }
    }

    /**
     * Logs in a user by cookie.
     *
     * This method attempts to log in a user using the ID and authKey information
     * provided by the [[identityCookie|identity cookie]].
     */
    protected function loginByCookie()
    {
        $data = $this->getIdentityAndDurationFromCookie();
        if (isset($data['identity'], $data['duration'])) {
            $identity = $data['identity'];
            $duration = $data['duration'];
            if ($this->beforeLogin($identity, true, $duration)) {
                $this->switchIdentity($identity, $this->autoRenewCookie ? $duration : 0);
                $id = $identity->getId();
                $ip = Yii::$app->getRequest()->getUserIP();
                Yii::info("User '$id' logged in from $ip via cookie.", __METHOD__);
                $this->afterLogin($identity, true, $duration);
            }
        }
    }

    /**
     * Determines if an identity cookie has a valid format and contains a valid auth key.
     * This method is used when [[enableAutoLogin]] is true.
     * This method attempts to authenticate a user using the information in the identity cookie.
     * @return array|null Returns an array of 'identity' and 'duration' if valid, otherwise null.
     * @see loginByCookie()
     * @since 2.0.9
     */
    protected function getIdentityAndDurationFromCookie()
    {
        $value = Yii::$app->getRequest()->getCookies()->getValue($this->identityCookie['name']);
        if ($value === null) {
            return null;
        }
        $data = json_decode($value, true);
        if (count($data) == 3) {
            list ($id, $authKey, $duration) = $data;
            /* @var $class IdentityInterface */
            $class = $this->identityClass;
            $identity = $class::findIdentity($id);
            if ($identity !== null) {
                if (!$identity instanceof IdentityInterface) {
                    throw new InvalidValueException("$class::findIdentity() must return an object implementing IdentityInterface.");
                } elseif (!$identity->validateAuthKey($authKey)) {
                    Yii::warning("Invalid auth key attempted for user '$id': $authKey", __METHOD__);
                } else {
                    return ['identity' => $identity, 'duration' => $duration];
                }
            }
        }
        $this->removeIdentityCookie();
        return null;
    }

    /**
     * Removes the identity cookie.
     * This method is used when [[enableAutoLogin]] is true.
     * @since 2.0.9
     */
    protected function removeIdentityCookie()
    {
        Yii::$app->getResponse()->getCookies()->remove(new Cookie($this->identityCookie));
    }
```

在上面的AccessControl访问控制里面间接通过IsGuest属性来判断是否是认证用户，
然后在getIsGuest方法里面是调用getIdentity来获取用户信息，如果不为空就说明是认证用户，
否则就是游客（未登录）。

renewAuthStatus 重新生成用户认证信息。这一部分先通过session来判断用户，
因为用户登录后就已经存在于session中了。然后再判断如果是自动登录，那么就通过cookie信息来登录。

通过保存的Cookie信息来登录 loginByCookie。

先读取cookie值，然后$data = json_decode($value, true);反序列化为数组。

这个从上面的代码可以知道要想实现自动登录，这三个值都必须有值。另外，
在User Model中还必须要实现findIdentity、validateAuthKey这两个方法。

登录完成后，还可以再重新设置cookie的有效期，这样便能一起有效下去了。

```
$this->switchIdentity($identity, $this->autoRenewCookie ? $duration : 0);
```

#### 退出 logout

backend\controllers\SiteController中：

```
    /**
     * Logout action.
     *
     * @return string
     */
    public function actionLogout()
    {
        Yii::$app->user->logout();

        return $this->goHome();
    }
```

yii\web\User中：

```
    /**
     * Logs out the current user.
     * This will remove authentication-related session data.
     * If `$destroySession` is true, all session data will be removed.
     * @param bool $destroySession whether to destroy the whole session. Defaults to true.
     * This parameter is ignored if [[enableSession]] is false.
     * @return bool whether the user is logged out
     */
    public function logout($destroySession = true)
    {
        $identity = $this->getIdentity();
        if ($identity !== null && $this->beforeLogout($identity)) {
            $this->switchIdentity(null);
            $id = $identity->getId();
            $ip = Yii::$app->getRequest()->getUserIP();
            Yii::info("User '$id' logged out from $ip.", __METHOD__);
            if ($destroySession && $this->enableSession) {
                Yii::$app->getSession()->destroy();
            }
            $this->afterLogout($identity);
        }

        return $this->getIsGuest();
    }

    /**
     * Switches to a new identity for the current user.
     *
     * When [[enableSession]] is true, this method may use session and/or cookie to store the user identity information,
     * according to the value of `$duration`. Please refer to [[login()]] for more details.
     *
     * This method is mainly called by [[login()]], [[logout()]] and [[loginByCookie()]]
     * when the current user needs to be associated with the corresponding identity information.
     *
     * @param IdentityInterface|null $identity the identity information to be associated with the current user.
     * If null, it means switching the current user to be a guest.
     * @param int $duration number of seconds that the user can remain in logged-in status.
     * This parameter is used only when `$identity` is not null.
     */
    public function switchIdentity($identity, $duration = 0)
    {
        $this->setIdentity($identity);

        if (!$this->enableSession) {
            return;
        }

        /* Ensure any existing identity cookies are removed. */
        if ($this->enableAutoLogin) {
            $this->removeIdentityCookie();
        }

        $session = Yii::$app->getSession();
        if (!YII_ENV_TEST) {
            $session->regenerateID(true);
        }
        $session->remove($this->idParam);
        $session->remove($this->authTimeoutParam);

        if ($identity) {
            $session->set($this->idParam, $identity->getId());
            if ($this->authTimeout !== null) {
                $session->set($this->authTimeoutParam, time() + $this->authTimeout);
            }
            if ($this->absoluteAuthTimeout !== null) {
                $session->set($this->absoluteAuthTimeoutParam, time() + $this->absoluteAuthTimeout);
            }
            if ($duration > 0 && $this->enableAutoLogin) {
                $this->sendIdentityCookie($identity, $duration);
            }
        }
    }

    /**
     * Removes the identity cookie.
     * This method is used when [[enableAutoLogin]] is true.
     * @since 2.0.9
     */
    protected function removeIdentityCookie()
    {
        Yii::$app->getResponse()->getCookies()->remove(new Cookie($this->identityCookie));
    }
```

退出的时候先把当前的认证设置为null，然后再判断如果是自动登录功能则再删除相关的cookie信息。

#### 总结

auth_key是用来做cookie自动登录用的，在创建用户时第一次生成，之后基本不会再修改。

```
    /**
     * Signs user up.
     *
     * @return User|null the saved model or null if saving fails
     */
    public function signup()
    {
        if (!$this->validate()) {
            return null;
        }
        
        $user = new Adminuser();
        $user->username = $this->username;
        $user->nickname = $this->nickname;
        $user->email = $this->email;
        $user->profile = $this->profile;
        $user->setPassword($this->password);
        $user->generateAuthKey();
//        $user->generatePasswordResetToken();  // 初次添加时为空，在找回密码发送邮件时会用到
        $user->password = $user->password_hash;
//        $user->save(); VarDumper::dump($user->errors);exit(0);
        
        return $user->save() ? $user : null;
    }
```

#### 注意

用户C在登录状态，管理员A修改了C的密码和auth_key，则C现在还可以访问网站不？

如果服务器上还有C的session，则C还可以访问网站，暂时与C的cookie无关，
如果服务器上没有C的session，则C需要用cookie自动登录，但是数据库中C的auth_key已经变了，
cookie自动登录不成功，也不能访问网站了。

<br/><br/><br/><br/><br/>
### 参考资料 

<http://www.yii-china.com/post/detail/323.html>

Yii2 csrf综合 <https://ibaiyang.github.io/blog/yii2/2017/12/03/Yii2-csrf%E7%BB%BC%E5%90%88.html>

