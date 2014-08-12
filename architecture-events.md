# 架构和事件

<p class="uk-article-lead">了解Pagekit的内部结构以及各组件的连接</p>

要获得Pagekit的文件和文件夹的概述，请参阅[文件夹结构](folder-structure.md) ，作为一名开发人员您可能对`vendor`文件夹感兴趣。在此文件夹，你会发现所有的代码，除了从引导码，如：

- JavaScript 和 CSS 库, 如 jQuery, UIKit 等
- 第三方PHP 库，如 Symfony, Doctrine 等
- Razr 模版引擎, Pagekit的组件和Pagekit的架构

## 架构

### 应用程序

Pagekit的核心是应用程序，也就是一个`Pagekit\Framework\Application`应用实例，该应用程序被用来接受请求并返回一个响应（更多是在*请求生命周期*部分）。它拥有一个`Router`对象，位于`HttpKernel`最顶部的它处理最低水平的请求。

该应用程序也可被视为一个[Pimple依赖注入容器](http://pimple.sensiolabs.org/), 保存服务提供者的位置, 服务提供者是一个中心的概念，因为他们捆绑某些组件和框架的其他部分的功能，并使其在整个应用程序中访问 - 也从你的扩展和主题中访问。

### 服务提供商

服务提供商是实现`register`和`boot`的一个类， `register` 将在应用程序的引导阶段被调用，`boot`在应用程序接收到请求时被调用。`register`将预期的服务分配给应用程序，如`$app['db']`提供从代码的任何一个地方访问数据库的功能

系统不同部分的通信以事件形式传导，更多信息请参见下方*事件*部分。

## 请求的生命周期

为了更深层的了解Pagekit的内部控制流程，让我们来看看当一个请求经过Pagekit所有的层时会发生什么。

当web服务器在每次请求中被调用时，`index.php`都是入口点。在这里，两个基本阶段首先正式拉开序幕。首先，`/app/app.php`对于内部Pagekit的内部控制流程有更深的了解，让我们来看看当一个请求经过的所有Pagekit层会发生什么。 

所有请求处理的入口点是`index.php`文件在每次请求的Web服务器调用。在这里，两个基本阶段得到正式拉开序幕。首先，`/应用程序/ app.php`被加载，它包含设置应用程序所需的所有引导代码，第二个阶段是请求处理阶段，让我们分开看看这两个阶段。

### 1. 引导阶段

启动发生在`/app/app.php`并会读取在 `/app/config/app.php`中的默认配置, 与`config.php`产生一个数组后共同传递给一个新的应用程序实例。

然后，在配置中定义的所有服务供应商在应用程序中被注册，之后建立到数据库的连接并加载生成插件队列（实际负荷发生在第二阶段）。 

如果`config`文件丢失或无法连接到数据库，这就说明Pagekit还未安装，这时加载的插件只有*Installer*，这将导致安装程序在第二阶段结束的时候被显示。

### 2. 请求处理阶段

启动结束后，应用程序将以run的方式处理http请求从应用程序的角度来看，请求处理将由Router进行。

`Router` 位于 `HttpKernel`之上, 取决于路径评价的结果是调度呼叫控制器的体系结构的最低层。内核将返回一个响应，这意味着它会提供意见和字符串成一个响应或抛出要返回一个异常。这种反应然后被交还给可以修改响应对象的最上层。

## 事件

系统间通信的核心组建是 `EventDispatcher` ，他可以通过 `$app['events']` (或
`$this['events']` 在使用ApplicationTrait时)。每一部分都能监听某些事件和触发一些事件。

订阅事件，一个类必须实现`EventSubscriberInterface`并同拖静态方法`getSubscribedEvents`，这将返回一个数组，其订阅的项和回调函数作为它的值。

```php
<?php

namespace Pagekit\Hello\Event;

use Pagekit\Framework\Event\EventSubscriber;

class HelloListener extends EventSubscriber
{
    public function onBoot($event, $eventName, $dispatcher)
    {
        // do something
    }

    public function anyName() // omit parameters if you do not need them
    {
        // do something
    }

    public static function getSubscribedEvents()
    {
        return [

            'hello.boot' => 'onBoot',

            // use any name and add a priority int if desired
            'hello.boot' => ['anyName', 10]
        ];
    }
}
```

在您插件（或主题）的 `boot` 部分注册你的监听器，以便它可以订阅事件。

```php
$this['events']->addSubscriber(new HelloListener());
```

您可以使用应用的 `on` 快捷方式来对事件作出反应也可以只传递一个闭包。

```php
$app->on('hello.boot', function($event) use ($app){
    // do something
});
```

调度事件使用以下语法。

```php
// dispatch event, optional second $event parameter
$app['events']->dispatch('hello.boot');
```

### 事件类型 

有许多不同的事件类型，很多你遇到的事件实际上来自内核, 你可以在 `Symfony\Component\HttpKernel\KernelEvents`. 找到他们的详细说明。由于每个组建可以在任何给定时间调度事件, 那里将会有很多不同的事件

**注意** 一个强大的可以查看所有事件的插件 *profiler toolbar* 您可以在Pagekit后台启动它。
