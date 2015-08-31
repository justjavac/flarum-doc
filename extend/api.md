# API

正如介绍所述，Flarum 有一个**公开的 JSON API 允许你读写论坛数据**。它符合 [JSON-API 1.0 规范](http://jsonapi.org.cn)。

在本节中我们将深入探讨如何扩展 Flarum 的 API。

## JSON-API 和序列化程序（Serializers)

Flarum 使用 [tobscure/json-api](https://github.com/tobscure/json-api) 库来输出符合 JSON-API 规范的格式化数据。这涉及序列化程序的使用，该程序是 `Flarum\Api\Serializers` 命名空间下的一个类，能将 Flarum 的数据转换成一个可被公开接受的格式。

### 属性

可使用 `ApiAttributes` 事件对序列化程序进行增改：

```php
use Flarum\Api\Serializers\DiscussionSerializer;
use Flarum\Events\ApiAttributes;

$events->listen(ApiAttributes::class, function (ApiAttributes $event) {
    if ($event->serializer instanceof DiscussionSerializer) {
        $event->attributes['isSticky'] = (bool) $event->model->is_sticky;
        $event->attributes['canSticky'] = $event->model->can($event->actor, 'sticky');
    }
});
```

### 关系

可使用 `ApiRelationship` 事件来添加新的关系模型。Flarum 的底层 Serializer 类提供了两个方便的方法( `hasOne` 和 `hasMany`) ，可构建一个 `Tobscure\JsonApi\Relationship` 对象，来利用被序列化程序处理好的模型。

```php
use Flarum\Api\Serializers\DiscussionSerializer;
use Flarum\Events\ApiRelationship;

$events->listen(ApiRelationship::class, function (ApiRelationship $event) {
    if ($event->serializer instanceof DiscussionSerializer && $event->relationship === 'tags') {
        return $event->serializer->hasMany('Flarum\Tags\Api\TagSerializer');
    }
});
```

为使这些关系能被对应文档所链接或包含，你必须把关系与适当的行为加以联系 ―― 阅读下面。

## 行为

Flarum 的 API 端点(endpoint)是在 `Flarum\Api\ApiServiceProvider` 中定义的。每个端点都与一个对应的**行为（Action）**类以路由的方式加以连接，该类实际上是一个控制器。这个行为类应能接受一个 `Flarum\Api\Request` 对象，并以 PSR-7 格式响应。

在 `Flarum\Api\Action` 命名空间下有大量的抽象行为类，可被扩展以轻易地实现数据库操作（译注：原文作 CRUD actions, 请参见[维基百科](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete)）。其中包括 `SerializeResourceAction` 和 `SerializeCollectionAction` 类，这两个类将为你进行设置和在序列化程序中传递数据的所有工作。初学者仅需实现 `data` 方法并以 Eloquent 模型（译注：此为 Laravel 功能，请参见[官方文档](http://laravel.com/api/5.0/Illuminate/Database/Eloquent/Model.html)） 或集合(Collection)形式返回即可。

### 更改行为属性

`data` 方法接受一个请求对象和几个额外的参数：`sort`，`include`，`link`，`limit` 和 `offset`。这些参数使用行为类中的配置来进行处理，这些配置可被扩展程序使用 `BuildApiAction` 事件进行修改，以允许添加新的关系、新的可排序字段，诸如此类。 

```php
use Flarum\Api\Actions\Discussions;
use Flarum\Events\BuildApiAction;

$events->listen(BuildApiAction::class, function (BuildApiAction $event) {
    if ($event->action instanceof Discussions\IndexAction ||
        $event->action instanceof Discussions\ShowAction ||
        $event->action instanceof Discussions\CreateAction ||
        $event->action instanceof Discussions\UpdateAction) {
        // Include the tags relationship on discussion resources by default
        $event->addInclude('tags');
    }
});
```

### 添加 API 端点

如果您需要添加一个新的 API 端点，可从任何 Action 基类加以扩展，然后使用 `RegisterApiRoutes` 事件注册新路由即可：

```php
use Flarum\Events\RegisterApiRoutes;

$events->listen(RegisterApiRoutes::class, function (RegisterApiRoutes $event) {
    $event->get(
        '/tags', // The endpoint URI
        'api.tags.index', // A unique name for the endpoint
        'Flarum\Tags\Api\IndexAction' // The Action class to handle the request
    );
});
```
> 译者：[@ttnl](https://github.com/ttnl)