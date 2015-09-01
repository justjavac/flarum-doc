# 打包

## 配置

在你开始写扩展之前，你需要在系统上配置几个工具：

* 下载并**全局**安装 [Composer](https://getcomposer.org) ，为扩展生成自动加载。
* 配置 [Node.js](https://nodejs.org) 并全局安装 [Gulp](http://gulpjs.com) ，编译扩展的客户端 JavaScript。

除此之外，你也可以安装 Flarum 的 [Vagrant 开发者镜像](../preface/contributing.md)，它帮你自动装好了所有工具。

## 生成框架

Flarum 有便利的工具来为你生成扩展的框架，这样你就可以马上开始写代码了。打开终端并在你论坛根目录下运行下面的命令：

```bash
php flarum/flarum generate:extension
```

工具会向你咨询一些细节。扩展名是你扩展独一无二的名称，只能包含数字、字母和连字符(`-`).命名空间用来分离你的代码；你可以把提供者名字加扩展名(例如 `Tobscure\Attachments` )作为命名空间.

一旦你填完所有细节，工具会在你论坛根目录的 `extensions` 目录下创建一个新目录。完成这些可能会花费几分钟的时间。下面让我们看下生成了些什么：

* **bootstrap.php** 如果你的扩展被启用， Flarum 会在每当有请求时加载这个文件。这个文件是扩展启动的地方！你不应该修改它。
* **flarum.json** 包含你扩展的元信息(标题、描述、作者、依赖项)。更多信息请往下看。
* **js/** 存放扩展的 JavaScript 文件。你将在[扩展客户端](client.md)了解到更多。
* **less/** 存放扩展的 CSS 文件。你将在[主题](theming.md)了解到更多。
* **locale/** 存放扩展的翻译文件。你将在[本地化](localization.md)了解到更多。
* **migrations/** 当扩展被安装/升级/卸载时会运行数据迁移。你将在[域扩展](domain.md)了解到更多。
* **src/** 存放扩展的后端源代码，将被使用 PSR-4 标准的 Composer 自动加载。

## flarum.json

文档待写。

## 事件订阅者

就像你从介绍里了解到的一样，扩展工作的唯一方式就是**监听和响应事件**。让我们看下这是如何实现的。

我们将从头开始。如果你看过 `bootstrap.php` ，你会发现它返回一个扩展类的名字。如果扩展启用了，在每个请求的开始，Flarum 会包含这个文件并且把这个类注册为一个[服务提供者](http://laravel.com/docs/5.1/providers)。这个类是你处理事务的关键。

我们再继续看，Flarum 在 `src` 目录下生成了这个类。如果你仔细看，你会发现这个独立的方法:

```php
public function listen(Dispatcher $events)
{
    $events->subscribe('Your\Namespace\Listeners\AddClientAssets');
}
```

当事件系统建立起来后，Flarum 就会调用每个扩展服务提供者的 `listen` 方法。它传递一个 [Laravel 的消息分发](http://laravel.com/docs/5.1/events)实体，因此你能如你所愿得监听事件。

但我们在这做了些不同的事。我们调用 `subscribe` 方法去注册一个事件订阅者而不是直接添加事件监听器. **订阅者**是**分组相关事件监听器**的一种，它能帮助你组织代码。你可以按你喜欢的方式组织你的事件监听器，但 Flarum 的附带扩展采用**订阅者的任务 + 订阅者名**(例如 `AddClientAssets` 或 `AddApiAttributes` )的约定。

好了，让我们再看下生成的 `AddClientAssets` 监听器。

```php
use Flarum\Events\RegisterLocales;
use Flarum\Events\BuildClientView;
use Illuminate\Contracts\Events\Dispatcher;

class AddClientAssets
{
    public function subscribe(Dispatcher $events)
    {
        $events->listen(RegisterLocales::class, [$this, 'addLocale']);
        $events->listen(BuildClientView::class, [$this, 'addAssets']);
    }
```

**这**是我们注册事件监听器的地方。我们导入 event 类，然后在订阅者的 `subscribe` 方法里调用分发器的 `listen()` ， 传递的参数是我们希望订阅的事件名称和处理事件的方法。

这些特定的事件监听器用来处理默认的本地化语言包、JavaScript 和 CSS 文件，这些都是在创建扩展的时候由系统自动生成。你可以在[扩展客户端](client.md)章节了解到关于他们得更多信息。

> 译者：[@theFool32](https://github.com/theFool32)