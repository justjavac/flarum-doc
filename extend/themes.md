# 主题

Flarum将编译LESS文件到CSS文件，然后担任客户端的一部分，关于论坛的定制很容易，在管理员面板外观部分可以通过点击“编辑自定义CSS”添加你自己的LESS/CSS。

## 编译资源

扩展框架包含一个空的`LESS/extension.less`文件，你可以把任何自定义的LESS/CSS样式写入这个文件。这个文件使用的是`BuildClientView`事件注册：

```php
use Flarum\Events\BuildClientView;

$events->listen(BuildClientView::class, function (BuildClientView $event) {
    $event->forumAssets([
        __DIR__.'/../../less/extension.less'
    ]);
});
```

## 变量

有许多LESS定义主题颜色和其他的值贯穿整个LESS的变量。它们被列在[variables.less](https://github.com/flarum/core/blob/master/less/lib/variables.less)。你应该使用这些变量来实现论坛的配色方案到您的扩展。

## 规范

Flarum 使用的是BEM风格CSS类的命名规则

    .ComponentName-child--modifier
    
该组件名称组成部分应该与存在的JavaScript组件的名称保持一致。

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