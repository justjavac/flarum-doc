# 故障诊断

## 安装

如果你在安装 Flarum 的过程中遇到了问题，先去论坛的 [Installation 标签](http://discuss.flarum.org/t/installation)看一下。 是否有人和你遇到了同样的问题! 如果没有找到，发布一个新话题，我们会尽力帮助你解决问题。

（译者注：在官方论坛提问时，请尽量使用英语，如果英语水平不好，可以选择在中文论坛提问。）

### 已知的问题

* 目前安装程序没有进行输入验证，如果您输入了无效的管理用户名，安装程序将默默地失败。用户名只能包含字母、数字、破折号、下划线。
* 如果 `flarum/storage/framework/views` 目录没有写入的权限，安装程序将崩溃。确保 PHP 对此目录有写权限。

## 调试

当你在使用论坛的过程中发生错误，要做的第一件事就是开启调试模式。使用文本编辑器打开 `config.php` 文件，将 `debug` 的值修改为 `true`。这样，当 Flarum 发生任何错误时，将会显示详细的错误消息。

如果你访问网站时看到一个空白页面，将 PHP 配置文件 php.ini 的 `display_errors` 选项设置为 `On`。 或者，将以下代码添加到 `flarum/bootstrap.php` 文件的开头：

```php
ini_set('display_errors', 'On');
```

**注：当错误修正后，一定要恢复这些选项！**

> 译者：[@justjavac](https://github.com/justjavac)