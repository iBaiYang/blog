---
layout: post
categories: Laravel
title: Laravel Contract契约
meta: Laravel Contract契约
---
* content
{:toc}

## 正文

下面详细、深入地剖析一下 Laravel 6 中的「契约」（Contracts）。

---

### 1. 什么是契约

在 Laravel 中，契约（Contracts）是一组由框架提供的、定义核心服务的接口（Interface）。

简单来说，它们就是 PHP 接口，但扮演着极其重要的角色。每一个契约都声明了一组方法，这些方法代表了框架提供的一项特定服务（例如日志、缓存、队列、邮件等）。

核心思想：
契约定义了 “做什么”（What），而具体的实现类（由 Laravel 或你自己提供）则定义了 “怎么做”（How）。这种设计将接口与实现完全分离开来。

一些常见的 Laravel 契约示例：
*   `Illuminate\Contracts\Cache\Factory` / `Illuminate\Contracts\Cache\Repository`： 定义了缓存服务的方法（如 `get`, `put`, `has`）。
*   `Illuminate\Contracts\Logging\Log`： 定义了日志服务的方法（如 `emergency`, `alert`, `error`, `info`）。
*   `Illuminate\Contracts\Mail\Mailer`： 定义了邮件服务的方法（如 `to`, `send`）。
*   `Illuminate\Contracts\Queue\Queue`： 定义了队列服务的方法（如 `push`, `later`）。
*   `Illuminate\Contracts\Filesystem\Filesystem`： 定义了文件系统操作的方法（如 `get`, `put`, `exists`, `delete`）。

---

### 2. 背景与设计目的

为什么需要契约？

契约的出现是为了解决几个关键的软件设计问题，其背景源于 依赖反转原则（Dependency Inversion Principle, DIP） 和 控制反转（Inversion of Control, IoC）。

1.  解耦与低耦合（Loose Coupling）
    *   没有契约时： 你的控制器或服务类可能直接依赖于一个具体的日志类，比如 `new FileLog()`。如果你的需求变了，想改用 `SlackLog`，你就必须修改所有使用了 `FileLog` 的代码。这违反了“对修改关闭，对扩展开放”的开闭原则。
    *   有契约时： 你的代码只依赖于一个接口（如 `LogContract`）。只要 `SlackLog` 和 `FileLog` 都实现了这个接口，你就可以在不修改任何业务代码的情况下，轻松切换实现。系统的各个部分变得独立，更容易维护和测试。

2.  可测试性（Testability）
    *   这是契约带来的巨大好处。在单元测试中，你经常需要“模拟”（Mock）或“存根”（Stub）外部服务（如数据库、邮件、日志）。
    *   由于你的代码依赖于清晰的接口，你可以非常容易地使用 PHPUnit 的 `Mockery` 或类似的工具创建一个该接口的模拟实现，从而隔离被测代码，进行纯粹的单元测试。

3.  清晰性与自我文档（Clarity & Self-documentation）
    *   一个类通过构造函数或方法注入依赖时，如果类型提示是具体的类（如 `MySqlDatabase`），开发者需要去查看这个类的文档才知道它有什么方法。
    *   如果类型提示是一个契约（如 `DatabaseContract`），开发者立刻就知道这个类依赖哪些方法，因为接口的方法定义就是它最清晰的API文档。

---

### 3. 原理

Laravel 如何实现契约？

Laravel 契约机制的实现核心是 服务容器（Service Container），它是一个强大的依赖管理工具。

其工作原理可以概括为以下几步：

1. 绑定（Binding）： 在框架启动时（通常在服务提供者 `ServiceProvider` 的 `register` 方法中），Laravel 会将一个接口（契约）绑定到一个具体的实现类上。
    * 例如，在 `Illuminate\Log\LogServiceProvider` 中，`Psr\Log\LoggerInterface`（Laravel 自己的契约继承自 PSR 标准）被绑定到了具体的日志器实例上。

2. 解析（Resolving）： 当你的代码运行时（例如，一个控制器方法被调用），如果你在构造函数或方法中类型提示了一个契约（接口），服务容器就会自动介入。
    * 容器会查找它之前存储的绑定关系，找到这个接口对应的具体实现类是哪一个。
    * 然后，容器会自动实例化这个具体类（或返回已存在的实例），并将其注入（Inject）到你的代码中。

这个过程就是依赖注入（Dependency Injection），由服务容器自动完成，你无需手动 `new` 任何具体的类。

简单流程：

`你的代码请求 Contract (Interface)` -> `服务容器` -> `查找绑定关系` -> `返回 Concrete Implementation (具体实现类)` -> `注入你的代码`

---

### 4. 如何使用

实战示例。

使用契约主要有两种方式：依赖注入 和 门面（Facades）。值得注意的是，每一个门面都有一个对应的契约。

#### 方式一：依赖注入

方式一：依赖注入（推荐方式）

这是最直接和推荐的方式，因为它能最大限度地实现解耦和可测试性。

示例：在控制器中使用缓存契约

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
// 引入缓存仓库契约
use Illuminate\Contracts\Cache\Repository as Cache;

class UserController extends Controller
{
    protected $cache;

    /**
     * 通过构造函数注入缓存实现。
     * Laravel 会自动从容器中解析出 Cache 接口对应的实现（通常是 Redis/Memcached 实现）
     */
    public function __construct(Cache $cache)
    {
        $this->cache = $cache;
    }

    /**
     * 显示用户列表。
     *
     * @return \Illuminate\Http\Response
     */
    public function index()
    {
        // 使用契约提供的方法，无需关心具体实现是 Redis 还是 Memcached
        $users = $this->cache->get('users', function () {
            return DB::table('users')->get();
        });

        return view('users.index', ['users' => $users]);
    }
}
```

示例：在方法中注入

你也可以在控制器方法中直接注入，而不是在构造函数中。

```php
use Illuminate\Contracts\Cache\Repository as Cache;

public function show(Request $request, Cache $cache, $id)
{
    $user = $cache->get('user:'.$id, function() use ($id) {
        return User::findOrFail($id);
    });

    return view('users.show', ['user' => $user]);
}
```

#### 方式二：使用Facade

方式二：使用门面（Facade）

门面为 Laravel 的服务提供了一个简单的静态接口。在底层，门面代理了对服务容器中已绑定实例的方法调用。

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Cache; // 使用 Cache 门面

class UserController extends Controller
{
    public function index()
    {
        // 静态调用，底层实际上是通过服务容器解析了契约对应的实现
        $users = Cache::get('users', function () {
            return DB::table('users')->get();
        });

        return view('users.index', ['users' => $users]);
    }
}
```

门面 vs. 契约（依赖注入）：
*   门面 更简洁，写起来更快，在简单的场景下很方便。
*   契约（依赖注入） 更显式，解耦更彻底，在测试时更容易模拟，是构建大型、复杂应用的首选。Laravel 官方文档也推荐使用依赖注入的方式。

#### 查看契约

如何查看契约及其对应的方法？

所有 Laravel 的契约都位于 `Illuminate/Contracts` 目录下。最好的方法是直接阅读这些接口的源代码，它们通常都非常简洁，只包含了方法的签名和注释，是绝佳的文档。

例如，查看 `Illuminate\Contracts\Cache\Repository`，你就能看到所有可用的缓存方法。

---

### 5. 自定义契约和实践

你不仅可以使用框架提供的契约，更应该在自己的项目中也创建契约，这是实现良好设计的关键。

场景：假设你有一个 `PaymentService` 用来处理支付，现在只接入了 Stripe。

1.  创建契约（定义“做什么”）

    ```php
    // app/Contracts/PaymentServiceContract.php
    namespace App\Contracts;

    interface PaymentServiceContract
    {
        public function charge($amount, $token);
        public function refund($paymentId);
    }
    ```

2.  创建实现类（定义“怎么做”）

    ```php
    // app/Services/StripePaymentService.php
    namespace App\Services;

    use App\Contracts\PaymentServiceContract;

    class StripePaymentService implements PaymentServiceContract
    {
        public function charge($amount, $token)
        {
            // Stripe 特定的支付逻辑
            // \Stripe\Charge::create(...);
        }

        public function refund($paymentId)
        {
            // Stripe 特定的退款逻辑
        }
    }
    ```

3.  在服务提供者中绑定契约和实现

    ```php
    // app/Providers/AppServiceProvider.php
    use Illuminate\Support\ServiceProvider;
    use App\Contracts\PaymentServiceContract;
    use App\Services\StripePaymentService;

    class AppServiceProvider extends ServiceProvider
    {
        public function register()
        {
            // 告诉容器，当有人请求 PaymentServiceContract 接口时，给它一个 StripePaymentService 实例
            $this->app->bind(PaymentServiceContract::class, StripePaymentService::class);
        }
    }
    ```

4.  在控制器中使用

    ```php
    // app/Http/Controllers/PaymentController.php
    use App\Contracts\PaymentServiceContract;

    class PaymentController extends Controller
    {
        public function pay(Request $request, PaymentServiceContract $paymentService)
        {
            // 现在控制器只依赖于我们自己定义的契约，完全不知道 Stripe 的存在
            $paymentService->charge($request->amount, $request->token);

            return redirect()->route('payment.success');
        }
    }
    ```

5.  未来扩展

如果未来需要接入 PayPal，你只需要：
* 创建一个 `PaypalPaymentService` 并实现 `PaymentServiceContract`。
* 在 `AppServiceProvider` 中修改绑定，将契约绑定到新的 `PaypalPaymentService`。
* 控制器和其他业务代码一行都不用改！ 这就是契约的强大之处。

### 总结

| 特性 | 描述 |
| :--- | :--- |
| **本质** | PHP 接口（Interface） |
| **目的** | **解耦**、提高**可测试性**、代码**清晰度** |
| **原理** | 基于 **服务容器（IoC Container）** 的依赖注入，将接口绑定到具体实现 |
| **使用方式** | 1. **依赖注入**（推荐，更灵活，易测试）<br>2. **门面**（简洁，底层也是契约） |
| **最佳实践** | 不仅使用框架契约，更要为自己的业务模块**自定义契约** |

Laravel 的契约是其架构中最优雅和强大的部分之一。它强制开发者面向接口编程，而不是面向实现编程，从而极大地提升了应用程序的质量和健壮性。理解和熟练运用契约，是成为一名高级 Laravel 开发者的必经之路。





## 参考资料

Laravel 6 中文文档 <https://learnku.com/docs/laravel/6.x/facades/5134>


