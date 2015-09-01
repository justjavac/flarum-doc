# 简介

Flarum 被设计成轻量级和高扩展的。事实上，Flarum 的大部分特性都是扩展。这种方式使得 Flarum 可定制性很强。用户可以禁用他不需要的特性，或者安装他们想要的扩展。

为了达到这种扩展性，Flarum 拥有丰富的接口和扩展点。文档该部分旨在指导你 Flarum 如何运作，如何使用接口构建自己的扩展。

> **扩展接口还不稳定，接下来几个月内，这种状况会随着我们的不断完善而改变。**另外，本文档也未完成。[我们需要你的反馈！](https://github.com/flarum/core/issues/246)

## 核心 vs 扩展

我们怎么界定 Flarum 的核心和扩展？为什么一些特性包含在了核心内而另一些却没有呢？理解这个差异对我们维持 Flarum 生态系统的一致性和高质量很重要。

**Flarum 的核心**并不意图去包含完整特性，相反，它是作为脚手架，或者说是框架，为扩展提供一个可依赖的构建基础。它只包含对一个论坛来说必要的、基础的、不可缺失的功能，这些功能有讨论、帖子、用户、群组和通知。

**绑定的扩展**是随 Flarum 一起打包的特性，默认情况下是开启的。这些扩展和其他扩展一样是可以禁用和卸载的。尽管他们并不旨在处理所有使用实例，但这些扩展还是很通用并且被配置得足够好，使得他们能够满足大部分情况。

**第三方扩展**是其他人制作的、不被 Flarum 团队官方支持的特性。他们应当被构建和用于特定的情形。

如果你想解决核心或者已有的附带扩展的 bug 或者缺陷，我们将很感激你对相应的项目作出贡献而不是分散精力在一个新的第三方扩展上。在[Flarum 论坛](http://discuss.flarum.org)上新建一个讨论来听听 Flarum 开发者的意见也是个好想法。

## Flarum 如何运作

在你构建扩展之前，你一定得先理解 Flarum 如何运作。本节旨在快速浏览组成 Flarum 的各部分。

从顶层来看，Flarum 有 3 层架构：

* **领域(domain)**层，代表所有 Flarum 数据结构和领域逻辑。
* **接口(API)**层，以一种标准格式(如 JSON API)暴露这些数据结构。
* **客户(client)**层，提供默认网络用户来使用接口。

为了构建扩展，你需要了解一点如何去扩展这些层中的每一个。例如**锁定(Sticky)**扩展:

* 扩展领域层，为讨论增加一个新的 `is_sticky` 属性。
* 扩展接口层，允许通过 JSON API 改变属性的值。
* 扩展客户层，增加一个讨论控制条目，该条目被点击时会调用 JSON API。

让我们更深入得了解下每个层，知道他们是如何构建的。

### 领域

领域层代表着**管理论坛数据和为论坛数据建模**。这包括用数据库存储数据、提供程序命令去修改数据和控制相关逻辑(例如当一个用户发帖时，我们在用户发帖数上加1)。

领域层使用**面向对象的 PHP**来构建，位于 `Flarum\Core` 这个命名空间里。领域层的每个实体(讨论、帖子等)都有自己的子命名空间。

领域层依赖 Laravel 的[数据库组件](http://laravel.com/docs/5.1/database)和 [Eloquent ORM](http://laravel.com/docs/5.1/eloquent)。每个实体代表一个 Eloquent 活动记录模型。这些模型对认证、许可、扩展有内建的机制。

为了提供一种修改数据的方式，Flarum 利用命令总线模式以及 Laravel 的[总线组件](http://laravel.com/docs/5.0/bus)。每个实体都有各种命令与其相关联(例如 `Flarum\Core\Discussions\Commands\StartDiscussion`, `Flarum\Core\Posts\Commands\PostReply`),这些命令都可以被调用来作用在 Flarum 的领域层上。

### 接口

接口层位于领域层之上，提供**能读写论坛数据的公有的 JSON API**。它符合 [JSON-API 1.0 规范](http://jsonapi.org.cn)

接口层使用**面向对象的 PHP**来构建，存放在`Flarum\Api`下。每个接口端都有相对应的**操作(Action)**(本质上是一个控制器)，这些操作接收请求对象然后返回响应。

大部分操作使用 [tobscure/json-api 库](https://github.com/tobscure/json-api)来输出一个 JSON-API 文档。来自 Flarum 领域层的数据通过序列化被转换成可使用的格式。写数据操作使用命令总线将命令发送到领域层。

### 客户 

客户层位于接口层之上，提供人们能使用 Flarum 的**用户界面**。客户层有两部分：一个基础的只读内容的 HTML 版本，和一个交互的单页 JavaScript 应用。两者都使用 JSON API.

客户层 HTML 利用**面向对象的 PHP**，存放在 `Flarum\Forum` 下。和接口层相似，每个论坛的路由（route）有一个对应的**操作**(本质是控制器)，这个操作能接收 [PSR-7](https://github.com/php-fig/http-message) 请求并返回响应.

典型的客户端响应包含一个表现页面内容的基础 HTML 样式，和 JavaScript 应用的启动代码。它使用 Laravel 的 [视图组件](http://laravel.com/docs/5.1/views)和 [Blade 模板系统](http://laravel.com/docs/5.1/blade)来构建。

JavaScript 应用基于 [Mithril](http://mithril.js.org) 开发，Mithril 是一个和 [React](http://facebook.github.io/react) 相似的轻量级渲染框架。JavaScript 应用利用 [Gulp](http://gulpjs.com) 和 [Babel](https://babeljs.io) 从 ES6 代码库转换而来。

客户层还包含一个 [LESS CSS](http://lesscss.org) 编译器来为客户端应用编译样式表。

## 扩展如何运作

现在你已经理解了 Flarum 如何运作，让我们来看看关于如何扩展 Flarum。

### 事件

在 Flarum 后端的 PHP 代码（例如领域层、接口层、部分客户层）中，有许多**事件**，而扩展可以监听这些事件，或者对事件作出响应。例如，开始一个讨论后，`DiscussionWasStarted` 事件被触发。扩展可以监听这个事件，这样当一个讨论开始后就能发送某种通知。

在 `Flarum\Events` 命名空间下的所有事件都能被监听.

监听事件很简单，只需要在事件调度程序中调用 `listen()` 方法，并传给它一个事件的类名和控制事件的闭包。你能在[打包](packaging.md)章节学习到更多关于如何注册事件监听者。

### 猴子补丁

扩展 Flarum 的前端 JavaScript 应用采用了一种叫[猴子补丁](https://en.wikipedia.org/wiki/Monkey_patch)的概念.猴子补丁可以在运行时替代一些原有的函数、方法和属性。

Flarum 的前端用户界面有许多组件组成，这些组件都是一个类。扩展可以用猴子补丁的方式修改这些类的方法，适当改变返回值。例如，一个扩展可以猴子补丁 `DiscussionControls` 类的 `moderationControls` 方法来添加一个新的锁定谈论的按钮.

你将会在[扩展客户端](client.md)章节学到更多关于如何在 Flarum 的 JavaScript 应用中运用猴子补丁。

> 译者：[@theFool32](https://github.com/theFool32)