# 简介

Flarum被设计成轻量级和高扩展的。事实上，Flarum的大部分特性实际是扩展。这种方式使得Flarum可定制性很强。用户可以禁用他不需要的特性和安装能给他们想要功能的扩展。

为了达到这种扩展性，Flarum被实现成有丰富接口和扩展点。文档该部分旨在指导你Flarum如何运作和如何去使用接口构建你自己的扩展。

> **扩展接口还不稳定并且会在接下来几个月内随着我们完善它而改变。**另外，本文档也未完成。[我们需要你的反馈！](https://github.com/flarum/core/issues/246)

## 核心 vs 扩展

我们怎么界定Flarum的核心和扩展？为什么一些特性被包含在核心内而另一些不呢？理解这个差异对我们维持Flarum生态系统的一致性和高质量很重要。

**Flarum的核心**并不意图去包含完整特性，相反，它是作为脚手架，或者说是框架，为扩展提供一个可依赖的构建基础。它只包含对一个论坛来说必要的基础的、不可缺失的功能，这些功能有讨论、帖子、用户、群组和通知。

**附带的扩展**是随Flarum一起打包的特性，默认情况下是被启用的。这些扩展和其他一样是可以被禁用和卸载的。尽管他们并不旨在处理所有使用实例，但这些扩展还是很通用并且被配置得足够好使得他们能够满足大部分情况。

**第三方扩展**是其他人制作的、不被Flarum团队官方支持的特性。他们应当被构建和用于特定的情形。

如果你想解决核心或者已有的附带扩展的bug或者缺陷，我们将很感激你对相应的项目作出贡献而不是分散精力在一个新的第三方扩展上。在[Flarum论坛](http://discuss.flarum.org)上新建一个讨论来听听Flarum开发者的意见也是个好想法。

## Flarum如何运作

在你构建扩展之前，你一定得先理解Flarum如何运作。本节旨在快速浏览组成Flarum的各部分。

从顶层来看，Flarum有3层架构：

* **领域(domain)**层，代表所有Flarum数据结构和领域逻辑。
* **接口(API)**层,以一种标准格式(如JSON API)暴露这些数据结构。
* **客户(client)**层,提供默认网络用户来使用接口。

为了构建扩展，你需要了解一点如何去扩展这些层中的每一个。例如*锁定(Sticky)*扩展:

* 扩展领域层,为讨论增加一个新的`is_sticky`属性。
* 扩展接口层，允许通过JSON API改变属性的值。
* 扩展客户层，增加一个讨论控制条目，该条目被点击时会调用JSON API。

让我们更深入得了解下每个层，知道他们是如何被构建的。

### 领域

领域层代表着**管理论坛数据和为论坛数据建模**。这包括用数据库存储数据、提供程序命令去修改数据和控制相关逻辑(例如当一个用户发帖时，我们在用户发帖数上加一)。

领域层使用**面向对象的PHP**来构建，位于`Flarum\Core`这个名称空间里.领域层的每个实体(讨论、帖子等)都有自己的子名称空间。

领域层依赖Laravel的[数据库组件](http://laravel.com/docs/5.1/database)和[Eloquent ORM](http://laravel.com/docs/5.1/eloquent)。每个实体被一个Eloquent活动记录模型代表。这些模型对认证、许可、扩展有内建的机制。

为了提供一种修改数据的方式，Flarum利用命令总线模式以及Laravel的[总线组件](http://laravel.com/docs/5.0/bus)。每个实体都有各种命令与其相关联(例如 `Flarum\Core\Discussions\Commands\StartDiscussion`, `Flarum\Core\Posts\Commands\PostReply`),这些命令都可以被调用来作用在Flarum的领域层上。

### 接口

接口层位于领域层之上，提供**能读写论坛数据的公有的JSON API**。它符合[JSON-API 1.0 规范](http://jsonapi.org)

接口层使用**面向对象的PHP**来构建,存放在`Flarum\Api`下。每个接口末端有相对应的**操作(Action)**(本质上是一个控制器)，这些操作接收请求对象然后返回响应。

大部分操作使用[tobscure/json-api库](https://github.com/tobscure/json-api)来输出一个JSON-API文档。来自Flarum领域层的数据通过序列化被转换成可使用的格式。写数据操作使用命令总线将命令发送到领域层。

### 客户

客户层位于接口层智商，提供人们能使用Flarum的**用户界面**。客户层有两部分：一个基础的只读内容的HTML版本，和一个交互的单页JavaScript应用。两者都使用JSON API.

客户层HTML利用**面向对象的PHP**，存放在`Flarum\Forum`下。和接口层相似，每个论坛的路径有一个对应的**操作**(本质是控制器)，这个操作能接收[PSR-7](https://github.com/php-fig/http-message)请求并返回响应.

典型的客户端响应包含一个表现页面的内容的基础HTML样式,和启动JavaScript应用的代码。它使用Laravel's [视图组件](http://laravel.com/docs/5.1/views)和[Blade模板系统](http://laravel.com/docs/5.1/blade)来构建。

JavaScript应用基于[Mithril](http://mithril.js.org)开发，Mithril是一个和[React](http://facebook.github.io/react)相似的轻量级渲染框架。JavaScript应用利用[Gulp](http://gulpjs.com)和[Babel](https://babeljs.io)从ES6代码库转换而来。

客户层还包含一个[LESS CSS](http://lesscss.org)编译器来为客户端应用编译样式表。

## 扩展如何运作

现在你已经理解了Flarum如何运作，让我们来看看关于如何扩展Flarum的基础概念。

### 事件

贯穿Flarum后端PHP代码(例如领域层、接口层、部分客户层)，有许多扩展可能
会监听和作出响应的**事件**。例如，开始一个讨论后，'DiscussionWasStarted'事件被触发。扩展可以监听这个事件，这样当一个讨论开始后就能发送某种通知。

在'Flarum\Events'名称空间下能找到的所有事件都能被监听.

监听事件很简单,只需要在事件调度程序中调用`listen()`方法,并传给它一个事件的类名和控制事件的闭包.你能在[打包]({{ site.baseurl }}/docs/extend/packaging)章节学习到更多关于如何去注册事件监听者的内容.

### 猴子补丁

扩展Flarum的前端JavaScript应用采用了一种叫[猴子补丁](https://en.wikipedia.org/wiki/Monkey_patch)的概念.猴子补丁是运行时函数、方法和属性的替代。

Flarum的前端用户界面有许多组件组成，他们中的每一个被定义为一个类。扩展可以用猴子补丁的方式修改这些类的方法，适当改变返回值。例如，一个扩展可以猴子补丁`DiscussionControls`类的`moderationControls`方法来添加一个新的锁定谈论的按钮.

你将会在[扩展客户端]({{ site.baseurl }}/docs/extend/client)章节学到更多关于如何在Flarum的JavaScript应用中运用猴子补丁的内容。
