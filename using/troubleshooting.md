# 故障诊断和报告

如果 Flarum 无法安装或者是没有按照预期运行，第一件需要做的事情就是*再次检查*你的环境是否达到了[系统要求](installation.md)。如果你缺失部分 Flarum 的依赖项（例如 PHP 的 `fileinfo` 扩展），你将需要先处理这些问题。
接下来，你应该花上几分钟在[支持论坛](http://discuss.flarum.org/t/support)和[问题追踪器](https://github.com/flarum/core/issues)内检索。有可能有人已经汇报了这个问题，或者解决方案正在讨论，或者已经有解决方案。在检索过后，如果你仍然没有发现关于这个问题的信息的话，就是需要进行故障诊断的时候了。

## 第一步：开启调试模式。

在你处理前，你应该开启 Flarum 的调试模式。仅需使用文本编辑器打开 `config.php` 文件，将 `debug` 的值修改为 `true`。这样，当 Flarum 发生任何错误时，将会显示详细的错误消息。

如果你访问网站时看到一个空白页面，将 PHP 配置文件 **php.ini** 的 `display_errors` 选项设置为 `On`。 或者，你也可以将以下代码添加到 **flarum/bootstrap.php** 文件的开头：
```php
ini_set('display_errors', 'On');
```
*当错误修正后，一定要恢复这些选项！*

## 第二步：复现错误。
试着让错误再发生一次。
> Re-translated by [@ttnl](https://github.com/ttnl) 17/1/21.
> First editon translated by @justjavac.
