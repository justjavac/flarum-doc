# 客户端

在[简介](introduction.md)一节中我们提到，客户端层暴露了一个供人类使用的**用户界面**。
客户端由两部分组成：一个只读的简单 HTML 页面和一个可交互的单页 JavaScript 应用。两者都从 [JSON API](http://jsonapi.org.cn) 读取数据。

## 路由

论坛所有的默认路由都在 `Flarum\Forum\ForumServiceProvider` 中注册。每个路由有一个对应的 **Action** （动作）类，
这个类本质上就是控制器。每个动作接受一个 [PSR-7](https://github.com/php-fig/http-message) 请求，并返回一个响应。

`Flarum\Forum\Actions\ClientAction` 是动作类的基类，它会构建一个 `ClientView` 实例，并将这个实例回应给客户端。
`ClientView` 是一个高度可定制的、包括前端 JavaScript 应用所需要的所有资源文件的主模板。例如你可以：

* 用 `setTitle()` 设置文档标题。
* 用 `setContent()` 设置页面的 SEO 内容（在 `<noscript>` 标签中显示的内容）。
* 用 `setDocument()` 设置页面中需要预加载的 API 响应。
* 用 `addHeadString()` 或 `addFootString()` 在页面的头部或尾部添加 HTML 内容。

新的论坛路由可以通过 `RegisterForumRoutes` 事件来注册。例如：

```php
use Flarum\Events\RegisterForumRoutes;

$events->listen(RegisterForumRoutes::class, function (RegisterForumRoutes $event) {
    $event->get(
        '/tags', // 路由 URI
        'forum.tags', // 路由的唯一名字
        'Flarum\Tags\Forum\TagsAction' // 响应请求的动作类（可选）
    );
});
```

定义响应请求的动作类（第三个参数）是可选的。如果没有指定，Flarum 会用默认的空白 `ClientAction` 来响应。

## JavaScript 应用

Flarum 最好的一部分就是运行飞快的交互式 JavaScript 前端应用。Flarum 的前端采用一个和
[React](http://facebook.github.io/react/) 很像的轻量级的渲染框架 [Mithril](http://mithril.js.org) 构建。
下面的内容假设你对 Mithrill 很熟悉，并且了解一些 React 的思想。

### 编译资源文件

Flarum 的前端 JavaScript 应用采用 [ES6](https://babeljs.io/docs/learn-es2015/) 编写，
并通过 [Gulp](http://gulpjs.com) 与 [Babel](https://babeljs.io) 转译成 ES5。
前端大量使用 [ES6 模块](https://babeljs.io/docs/learn-es2015/#modules)，
并转译成 [System.js 格式](https://github.com/systemjs/systemjs)。扩展的 JavaScript 也应当如此编写。

Flarum 为你生成的扩展框架包含了所有制作扩展所需的必要工具。
切换到 `js/forum` 目录，执行下面的命令就可以编译你扩展的 JavaScript 文件。

    gulp watch

编译后的 JavaScript 文件会输出到 `js/forum/dist/extension.js`。这个文件必须注册到客户端的资源管理器中，否则不会加载。
另外，还需要注册一个加载器，这样在前端 JS 应用启动的时候才会执行你的扩展代码。
你可以在 `BuildClientView` 事件中这么做（自动生成的框架中已经包含了这部分代码）：

```php
use Flarum\Events\BuildClientView;

$events->listen(BuildClientView::class, function (BuildClientView $event) {
    $event->forumAssets([
        __DIR__.'/../../js/forum/dist/extension.js'
    ]);

    $event->forumBootstrapper('extension-name/main');
});
```

### 运行时扩展（Monkey Patching）

在加载器中注册了你的 `main` 模块后，前端应用会在开始执行真正的逻辑前运行你在 `js/forum/src/main.js` 中写的代码。
这样，你的扩展就有机会修改你想要改的东西了。

在[简介](introduction.md)一节中我们曾一笔带过的提到我们可以通过 monkey-patching 替换函数。Flarum 提供了两个帮助你操作的函数。
这两个函数是 `extend` 和 `override` ，可以通过 `flarum/extend` 导入。调用这两个函数时，只需传入你想要修改的对象（通常来说是某个类的原型）、你想要修改的属性和一个回调函数即可。

`extend` 函数中，回调函数是在原来的方法 *运行完之后* 被调用的。它会把原来的执行结果传入你的回调函数，这样你可以修改返回值。

`override` 函数中，回调函数会 *替换原来的方法*。它会将原来的方法传入，如果你需要，可以在回调中随时调用它。

下面的这两个例子演示了这两个函数的使用方式：

```javascript
import {extend, override} from 'flarum/extend';

class Foo {
  aMethod() {
    return {
      color: 'red'
    };
  }

  bMethod() {
    return 'Gday';
  }
}

const foo = new Foo();
foo.aMethod(); // {color: 'red'}
foo.bMethod(); // 'Gday'

extend(Foo.prototype, 'aMethod', value => {
  value.color = 'yellow';
});

override(Foo.prototype, 'bMethod', original => {
  return original() + ' mate';
});

foo.aMethod(); // {color: 'yellow'}
foo.bMethod(); // 'Gday mate'
```

在运行时扩展方法的时候，你需要确认你扩展或者替换的方法和原来的方法签名（参数的数量和类型、返回值的类型）一致。

### 组件

组件是一些构建 Fluarm 的 UI 的可重用片段。你可以在
[这里](https://github.com/flarum/core/tree/master/js/forum/src/components) 找到所有的组件。

组件是在 [Mithril 的组件系统](http://mithril.js.org/mithril.component.html) 上用 React 的方式实现的。
一个组件必须继承 `flarum/Component` 并实现 `view()` 方法（默认的扩展工具会将 JSX 转义成 Mithril 的 `m()` 函数）。

```javascript
class WelcomeMessage extends Component {
  view() {
    return (
      <div className="WelcomeMessage">
        <h1>Hello, {this.props.name}!</h1>
      </div>
    );
  }
}
```

一个组件可以通过 `config()` 方法和它的根 DOM 元素互动。这个方法和 Mithril 的 `config` API 几乎是一样的实现，
只有一点不同：它没有传入第一个参数（`element`）。你可以用 `$()` 来操作这个元素，就像用一个 jQuery 对象一样。

```javascript
config(isInitialized, context) {
  if (isInitiailized) return;

  this.$().on('click', () => alert('clicked'));
}
```

### 对象列表（ItemList）

`ItemList` 类是一个通过名字来收集和整理对象的数组。例如：

```
import ItemList from 'flarum/utils/ItemList';

const items = new ItemList();

items.add('foo', 'Foo');
items.add('bar', 'Bar');
items.add('qux', 'Qux', 10); // 更高优先级的对象会排在前面

delete items.foo; // 删除一个对象

items.bar.content = 'Baz'; // 改变一个对象的内容

items.toArray(); // ['Qux', 'Baz']
```

许多组件和其它的实用工具会返回一个 `ItemList` 的实例（通常来说它们的方法名字以 Items 结尾）。
例如， `CommentPost` 组件的 `headerItems()` 方法就返回一个 `ItemList` 的实例，其中包含每条帖子的头部。
你可以用运行时扩展来修改它们：

```javascript
import CommentPost from 'flarum/components/CommentPost';

extend(CommentPost.prototype, 'headerItems', function(items) {
  items.add('location', '这个用户住在' + this.props.post.user().location());
});
```

### 应用全局变量

`app` 全局变量是 `flarum/ForumApp` 的实例，你可以通过这个变量来访问整个应用级别的组件实例和实用工具。

### 存储（Store）

存储（Store） 是一个和 Flarum 后端 API 交互的对象，可以通过 `app.store` 来访问。

存储会将在 API 响应中的资源和 Models 关联起来。Models 会让访问对象和关系变得容易起来。
例如，主题的 model 的一部分代码是这样的：

```javascript
class Discussion extends mixin(Model, {
  title: Model.attribute('title'),
  startTime: Model.attribute('startTime', Model.transformDate),
  startUser: Model.hasOne('startUser'),
  posts: Model.hasMany('posts'),
```

要通过 API 获得一个主题的 model，用 store 的 `find` 方法就行了。`find` 方法会返回一个 promise。例如：

```javascript
app.store.find('discussions', 1).then(discussion => {
  discussion.id(); // "1"
  discussion.title(); // "Discussion title"
  discussion.startUser(); // flarum/models/User 对象
});
```

你也可以用 `find` 方法进行更加复杂的查询，例如：

```javascript
app.store.find('discussions', {
  sort: '+time',
  page: {limit: 5},
  filter: {q: 'test'}
});
```

`find` 方法会无条件地发起一个 API 请求。如果你想要从 store 的本地缓存中获取 model 避免发起 API 请求，
那就得用 `getById` 和 `all` 这两个方法了。例如：

```javascript
const discussion = app.store.getById('discussions', 1);
const discussions = app.store.all('discussions');
```

Model 的属性和关系可以用 `save` 来存储到服务器，也可以用 `delete` 来删除。这两个方法都会返回一个 promise。例如：

```javascript
discussion.save({
  title: '新标题',
  relationships: {
    tags: [] // Tag model 的数组
  }
});
discussion.delete();
```

新的数据库记录也可以用 `createRecord` 方法来创建。例如：

```javascript
const discussion = app.store.createRecord('discussions', {
  title: '新的主题'
});
discussion.save();
```

### 路由

你可以通过 `app.routes` 来添加或修改客户端的路由。路由对象的键名是一个唯一的名字，
而值则是有 `path` 和 `component` 两个属性的对象。注意，修改路由只能在初始化的时候完成。

```javascript
app.routes.tag = {path: '/tag/{slug}', component: TagPage.component()};
```

你可以用 `app.route()` 来生成一个路由对应的 URL。例如：

```javascript
<a href={app.route('tag', {slug: 'blog'})} config={m.route}>博客</a>
```

> 译者：[@oott123](https://github.com/oott123)