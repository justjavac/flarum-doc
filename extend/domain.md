# 扩展域

如介绍所述，域(Domain)层负责**管理和抽象化论坛数据**。这包括在数据库中贮存数据，提供编程命令以修改数据，并处理相关逻辑（比如，当用户发了帖子，我们就在用户的帖子数统计上加一）。

在本节我们将深入探讨如何扩展 Flarum 的域。

## 迁移

如果您的扩展引入了一种新型的实体 (如标签)，或者将新属性添加到了一个现有的实体 (例如 `discussions` 的 `is_sticky` 属性)，那么你需要更新 Flarum 的数据库架构，以便新数据能被存储。

可通过**迁移（migrations）**来实现这一工作；Flarum 中的迁移基于 [Laravel的实现](http://laravel.com/docs/5.1/migrations)。迁移文件必须被放置在你扩展的 `migrations` 文件夹下，命名时应包括时间戳和对其所作修改的描述，如下所示：

	2015_02_24_000000_create_tags_table.php

此文件应该包括一个扩展自 `Flarum\Migrations\Migration` 的类，并附有相同的描述。每个迁移类包括两个方法：`up` 和 `down`。`up` 方法在你的扩展启用时便会启动（如果该迁移以前没有进行过），因此你可以添加在数据库中添加新的表、列、索引等等，亦可执行其他安装操作。`down` 操作在扩展卸载时被调用，应能够回滚 `up` 操作所执行的行为。

迁移可调用 `$schema` 变量，此为 [Laravel 的架构生成器](http://laravel.com/docs/5.1/migrations#writing-migrations)的一个实例，可被用来对数据库架构进行调整：

```php
use Illuminate\Database\Schema\Blueprint;
use Flarum\Migrations\Migration;

class CreateTagsTable extends Migration
{
    public function up()
    {
        $this->schema->create('tags', function (Blueprint $table) {
            $table->increments('id');
            $table->string('name', 100);
            $table->string('slug', 100);
            $table->text('description')->nullable();
        });
    }

    public function down()
    {
        $this->schema->drop('tags');
    }
}
```

如需在不重新启用某扩展运行新的迁移，只需简单地执行升级命令即可：

	php flarum upgrade

## 扩展模型

为了使用您设置的新数据库结构，你将需要创建新的模型(Model)或对现有的模型加以扩展。在Flarum 中，每个模型都扩展自 `Flarum\Core\Model`，此亦为 Laravel 的 `Eloquent\Model` 类的扩展。[在此详细了解 Eloquent 模型。](http://laravel.com/docs/5.1/eloquent)

### 属性

在 Eloquent 模型中，你无需为定义属性而烦恼。你可以简单的像在实例中的预设那样调用它，比如 `$discussion->is_sticky`。这一切使得事情变得很简单。

然而有一个例外：如果你添加了一个日期属性，你需要告诉模型此属性应被视为日期。可通过侦听 `ModelDates` 事件来实现：

```php
use Flarum\Core\Discussions\Discussion;
use Flarum\Events\ModelDates;

$events->listen(ModelDates::class, function (ModelDates $event) {
	if ($event->model instanceof Discussion) {
		$event->dates[] = 'stickied_at'; // stickied_at 被作为日期属性
	}
});
```

### 验证

每次保存模型时，其数据将根据使用 [Laravel 验证组件](http://laravel.com/docs/5.1/validation)以一套规则加以验证。这些规则可以被扩展所修改，所以您可以在模型属性上执行额外的验证。

```php
use Flarum\Core\Users\User;
use Flarum\Events\ModelValidator;

$events->listen(ModelValidator::class, function (ModelValidator $event) {
	if ($event->model instanceof User) {
		// Make usernames require at least 5 characters
        // username 至少 5 个字符
		$event->validator->mergeRules('username', 'min:5');
	}
});
```

### 关系

如果您需要对已有的模型定义一个新的关系，你可以使用 `ModelRelationship` 事件加以实现。每个未知的方法或属性连接模型时，都将调用此事件。如果你关系的名称与方法的名称匹配，你应该使之返回一个 Eloquent Relation 对象，该对象应使用模型 `hasOne`, `belongsTo`, `belongsToMany`, 或者 [其他关系方法](http://laravel.com/docs/5.1/eloquent-relationships)。

```php
use Flarum\Core\Discussions\Discussion;
use Flarum\Events\ModelRelationship;

$events->listen(ModelRelationship::class, function (ModelRelationship $event) {
	if ($event->model instanceof Discussion && $event->relationship === 'category') {
		return $event->model->belongsTo('category');
	}
});
```

## 数据持久化

如介绍中所述，Flarum 使用命令总线模式（译者注：请参见[本文档](http://laravel.com/docs/5.0/bus)。）提供对数据进行修改的方法。为了能够使一个核心实体中的所有新属性/关系保存到数据库，你需要注意会发生什么。

让我们用一个例子来说明这个过程。当我们对 `/discussions/123` 入口点进行 PATCH 请求时，将会进行下列过程：

1. 请求传递到合适的 API 行为类 (`Flarum\Api\Actions\Discussions\UpdateAction`)。
2. 该操作创建 `Flarum\Core\Discussions\Commands\EditDiscussion` 命令的新实例，并把请求输入进行封装。
3. 该行为把命令发送至命令总线，将其传递给适当的命令处理程序: `Flarum\Core\Discussions\Commands\EditDiscussionHandler`
4. `EditDiscussionHandler` 将基于命令的输入把更改应用于讨论模型，并保存此讨论。

在第四步时 `DiscussionWillBeSaved` 事件会被发送。这是在保存前，扩展用来检查输入、并对模型进行的附加修改的时机。

```php
use Flarum\Events\DiscussionWillBeSaved;

$events->listen(DiscussionWillBeSaved::class, function (DiscussionWillBeSaved $event) {
	if (isset($event->data['attributes']['isSticky'])) {
		$event->discussion->is_sticky = $event->data['attributes']['isSticky'];
	}
});
```

## 权限

Flarum 包括一个功能强大的权限系统。它使得扩展能自行定义操作逻辑，允许或不允许某些用户或组对某些实体执行特定行为。

### 模型锁

当你需要确定是否用户可以在模型上执行操作时，可调用该模型的 `can()` 方法 (在 `Flarum\Core\Support\Locked` 中定义) 。当用户被允许就返回 `ture`，反之则为 `false`。

```php
$allowed = $discussion->can($user, 'sticky');
```

扩展可以使用 `ModelAllow` 事件挂接于此，返回 `ture` 或 `false` 来授予或拒绝用户相关权限。

```php
use Flarum\Core\Discussions\Discussion;
use Flarum\Events\ModelAllow;

$events->listen(ModelAllow::class, function (ModelAllow $event) {
	if ($event->model instanceof Discussion && $event->action === 'sticky') {
		// Allow the user to sticky the discussion if they authored it
        // 允许作者置顶自己的讨论
		if ($event->actor->id === $event->model->user_id) {
			return true;
		}
	}
});
```

### 优先级

值得注意的是，当注册一个事件监听器时，你可以传递一个整数作为第三参数，从而给事件监听器对应的优先级。这对于 `ModelAllow` 事件相当有用，能使在返回第一个非空值时，停止执行后续的事件监听器。

### 组的权限

Flarum 具有关于组和对应授予的基本权限的映射（可在后台权限页加以修改）。此信息存储在数据库的权限表中，并可以在用户模型上使用 `hasPermission()` 方法访问:

```php
$granted = $user->hasPermission('discussion.sticky');
```

对管理员组的用户而言，此方法总是返回 `true`（也就是说，管理员有权进行一切事务）。其他情况，它将返回 `true` 或 `false`，具体取决于用户所在的组和对应权限映射。

这个系统与模块锁系统相连接，从而使得在被授权的某组中的用户能做规定的行为。默认情况下，在 `discussion` 模块上检查用户是否能做对应行为时，会检查其所在组的 `discussion.{action}` 许可。对于 `posts`、`users`和`groups`上亦如此。

若要了解如何在后台中添加权限设置，请参阅 [管理](admin.md) 章节。

### 可见范围

模块锁仅在检查已取得数据库的模型时有用。为了筛选哪种模式应该最先从数据库中取出 (比如确定哪些讨论/帖子是对用户可见的)，Flarum 使用一种机制称为**可见范围**。

可见范围只是一套能在查询数据库模型时可应用的条件语句。可以使用 `whereVisibleTo()` 方法应用此范围（该方法在`Flarum\Core\Support\VisibleScope`中定义）:

```php
// Olny get discussions which are visible to $user
// 仅获取对 $user 用户可见的帖子
$discussions = Discussion::whereVisibleTo($user)->get();
```

扩展可以使用 `ScopeModelVisibility` 事件挂接于此，应用限定条件于查询生成器：

```php
use Flarum\Core\Discussions\Discussion;
use Flarum\Events\ScopeModelVisibility;

$events->listen(ScopeModelVisibility::class, function (ScopeModelVisibility $event) {
	if ($event->model instanceof Discussion) {
		// Only let the user see discussions which they started
        // 用户只能看到自己发起的讨论
		$event->query->where('start_user_id', $event->actor->id);
	}
});
```

#### 帖子可见范围

A similar pattern is used to filter a discussion's posts to only those that are visible to the user:

我们使用一个简单的模式来筛选用户可见帖子：

```php
$posts = $discussion->postsVisibleTo($user)->get();
```

同样地，扩展可使用 `ScopePostVisibility` 事件来挂接到此方法，并对查询生成器应用限定条件。查询帖子的讨论在此事件传递。

> 译者：[@ttnl](https://github.com/ttnl)