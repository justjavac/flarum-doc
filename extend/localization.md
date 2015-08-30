# 本地化

## 翻译

扩展框架包括一个空的 `locale/en.yml` 文件，你可以把你的英语翻译放在这里。键名需要使用 `snake_case`。 他们应该放在和扩展名相同的 namespace 下。翻译可以包含 `{placeholders}`（占位符）。

（译者注：`snake_case` 是一种常见的命名法，他使用下划线来分割每个单词，比如 `register_locales`，通常用在 C 语言或者 javascript 中。对应的还有一种 CamelCase，顾名思义就是驼峰命名法，一般使用在 Java 中，比如 `RegisterLocales`）

```yaml
tags:
  tag_new_discussion_title: Choose Tags for Your Discussion
  edit_discussion_tags_title: "Edit Tags for {title}"
```

你可以使用 Flarum 的本地管理器（locale manager）的 `RegisterLocales` 事件注册这些语言包。扩展框架默认注册以下语言包：

```php
use Flarum\Events\RegisterLocales;

$events->listen(RegisterLocales::class, function (RegisterLocales $event) {
    $event->addTranslations('en', __DIR__.'/../../locale/en.yml');
});
```

为了使语言包能被编译成 JavaScript 文件，以便让 JavaScript 客户端调用，它们必须显式地通过 `BuildClientView` 事件注册。 如果你指定了一个键名，它所有的子元素也会被包括进来。

```php
use Flarum\Events\BuildClientView;

$events->listen(BuildClientView::class, function (BuildClientView $event) {
    $event->forumTranslations([
        'tags.tag_new_discussion_title',
        'tags.edit_discussion_tags_title'
    ]);
});
```

你需要从网站的 `assets` 目录里删除 `forum-en-xxxx.js`文件，可以强制性重新编译本地的 JavaScript。

翻译会调用客户端的 `app.trans()` 方法：

```javascript
app.trans('tags.edit_discussion_tags_title', {title: discussion.title()});
```

## 复式规则

如果一个 `count` 参数传递进来时，翻译器将寻找子节点的多个规则。默认的复式规则是英语：如果计数为 1，则查找 `one` 键，否则查找`other` 键：

```yaml
tags:
  choose_primary_tags:
    one: Choose a primary tag
    other: "Choose {count} primary tags"
```

```javascript
app.trans('tags.choose_primary_tags', {count: 1}); // 'Choose a primary tag'
app.trans('tags.choose_primary_tags', {count: 7}); // 'Choose 7 primary tags'
```

## 配置

您可以配置复式翻译系统的规则或者其他翻译器选项：通过给本地化管理器（locale manager）注册一个 JavaScript 配置文件：

```javascript
// locale/en.js
app.translator.plural = function(count) {
    return count === 1 ? 'one' : 'other';
};

// 你也可以做一些其它的事，比如：配置 moment.js 的本地化文件
```

```php
use Flarum\Events\RegisterLocales;

$events->listen(RegisterLocales::class, function (RegisterLocales $event) {
    $event->addJsFile('en', __DIR__.'/../../locale/en.js');
});
```

Flarum 国际化语言包 <https://github.com/justjavac/flarum-i18n-zh>

> 译者：[@justjavac](https://github.com/justjavac)