---
layout: post
categories: Yii2
title: Yii2 详说password_reset_token的用途
meta: Yii2 详说password_reset_token的用途
---
* content
{:toc}

### 正文

当我们用yii2开始项目时，在migrate初始生成user表时，会看到里面有一个password_reset_token字段，这个字段是做什么用的呢？查阅好多资料后，还是云里雾里，莫名其妙，不知所从。其实这个字段是在忘记密码情况下用来重设密码的。

登录页面，忘记密码了，点击reset it：

![](https://raw.githubusercontent.com/iBaiYang/PictureWareroom/master/20200417/20200417132771.jpeg)

跳转到：

![](https://raw.githubusercontent.com/iBaiYang/PictureWareroom/master/20200417/20200417132772.jpeg)

输入你的邮箱，然后去邮箱点击链接地址，重设密码就可以了。

我们看看输入邮箱的页面代码：

```php
public function actionRequestPasswordReset()
{
    $model = new PasswordResetRequestForm();
    if ($model->load(Yii::$app->request->post()) && $model->validate()) {
        if ($model->sendEmail()) {
            Yii::$app->session->setFlash('success', 'Check your email for further instructions.');
            
            return $this->goHome();
        } else {
            Yii::$app->session->setFlash('error', 'Sorry, we are unable to reset password for the provided email address.');
        }
    }
    
    return $this->render('requestPasswordResetToken', [
        'model' => $model,
    ]);
} 
```

我们看看PasswordResetRequestForm类的sendEmail()做了什么：

```php
public function sendEmail()
{
    $user = User::findOne([
        'status' => User::STATUS_ACTIVE,
        'email' => $this->email,
    ]);
    
    if (!$user) {
        return false;
    }
    
    if (!User::isPasswordResetTokenValid($user->password_reset_token)) {
        $user->generatePasswordResetToken(); // 生成唯一password_reset_token
        if (!$user->save()) {
            return false;
        }
    }
    
    return Yii::$app
    ->mailer
    ->compose(
        ['html' => 'passwordResetToken-html', 'text' => 'passwordResetToken-text'],
        ['user' => $user]
    )
    ->setFrom([Yii::$app->params['supportEmail'] => Yii::$app->name . ' robot'])
    ->setTo($this->email)
    ->setSubject('Password reset for ' . Yii::$app->name)
    ->send();
} 
```

这里就是生成唯一password_reset_token：

```php
public function generatePasswordResetToken()
{
    $this->password_reset_token = Yii::$app->security->generateRandomString() . '_' . time();
} 
```

并存入用户表，然后组装url发送给用户，url如下：

http://yii2_1108_frontend.host/index.php?r=site/reset-password&token=Asgm1LhqV4QXyg2sFyKrUjkNIx7jxugQ_1510203686

这里的token就是用户表的password_reset_token 。

我们看一下邮件：

![](https://raw.githubusercontent.com/iBaiYang/PictureWareroom/master/20200417/20200417132773.jpeg)

当我们点击这个链接时，跳转到设置密码页：

![](https://raw.githubusercontent.com/iBaiYang/PictureWareroom/master/20200417/20200417132774.jpeg)

我们看一下这一页的代码：

```php
public function actionResetPassword($token)
{
    try {
        $model = new ResetPasswordForm($token);
    } catch (InvalidParamException $e) {
        throw new BadRequestHttpException($e->getMessage());
    }
    
    if ($model->load(Yii::$app->request->post()) && $model->validate() && $model->resetPassword()) {
        Yii::$app->session->setFlash('success', 'New password saved.');
    
        return $this->goHome();
    }
    
    return $this->render('resetPassword', [
        'model' => $model,
    ]);
} 
```

我们看一下ResetPasswordForm类的构造方法：

```php
public function __construct($token, $config = [])
{
    if (empty($token) || !is_string($token)) {
        throw new InvalidParamException('Password reset token cannot be blank.');
    }
    $this->_user = User::findByPasswordResetToken($token);
    if (!$this->_user) {
        throw new InvalidParamException('Wrong password reset token.');
    }
    parent::__construct($config);
} 
```

以及resetPassword()方法：

```php
public function resetPassword()
{
    $user = $this->_user;
    $user->setPassword($this->password);
    $user->removePasswordResetToken();
    
    return $user->save(false);
} 
```


我们再看一下model类user的removePasswordResetToken()清除user用户表中的password_reset_token：

```php
public function removePasswordResetToken()
{
    $this->password_reset_token = null;
} 
```

这样这个重设密码的地址就只能用一次了，也没有留下漏洞。

然后你就可以用新密码登录了。

另外有一处，model类user的isPasswordResetTokenValid()方法：

```php
public static function isPasswordResetTokenValid($token)
{
    if (empty($token)) {
        return false;
    }
    
    $timestamp = (int) substr($token, strrpos($token, '_') + 1);
    $expire = Yii::$app->params['user.passwordResetTokenExpire'];
    return $timestamp + $expire >= time();
} 
```

这里会校验操作用户的password_reset_token，在找回密码时长内则不能再发送邮件；如果超出找回密码时长则不能重设密码。

<br/><br/><br/><br/><br/>
### 参考资料 





