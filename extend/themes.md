# 主题

Flarum 把 LESS 文件编译成 CSS 文件，然后担任客户端的一部分，关于论坛的定制很容易，在管理员面板外观部分可以通过点击“编辑自定义 CSS”添加你自己的 LESS/CSS。

## 编译资源

扩展框架包含一个空的 `LESS/extension.less` 文件，你可以把任何自定义的 LESS/CSS 样式写入这个文件。这个文件使用的是 `BuildClientView` 事件注册：

```php
use Flarum\Events\BuildClientView;

$events->listen(BuildClientView::class, function (BuildClientView $event) {
    $event->forumAssets([
        __DIR__.'/../../less/extension.less'
    ]);
});
```

## 变量

系统定义了许多 LESS 变量，包括定义主题颜色和其他。它们被列在 [variables.less](https://github.com/flarum/core/blob/master/less/lib/variables.less)。当你为扩展实现配色方案时，你应该使用这些变量。

## 规范

Flarum 使用 BEM 风格命名 CSS 类。

    .ComponentName-child--modifier
    
该组件名称组成部分应该与存在的 JavaScript 组件的名称保持一致。

## 模板

如果需要，您可以使用自定义模板更改论坛布局。(默认的位于[这里](https://github.com/flarum/core/blob/master/views/forum.blade.php)。）您的自定义模板必须包含所有相同的ID元素。
```php
use Flarum\Events\BuildClientView;
use Flarum\Forum\Actions\ClientAction as ForumClientAction;

$events->listen(BuildClientView::class, function (BuildClientView $event) {
    if ($event->action instanceof ForumClientAction) {
        $event->view->setLayout(__DIR__.'/../forum.blade.php');
    }
});
```
> 译者：[@Seevil](https://github.com/Seevil)