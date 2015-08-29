# 故障诊断

## 安装

如果你在安装 Flarum 的过程中遇到了问题，先去论坛的 [Installation 标签](http://discuss.flarum.org/t/installation)看一下。 是否有人和你遇到了同样的问题! 如果没有找到，发布一个新话题，我们会尽力帮助你解决问题。

（译者注：在官方论坛提问时，请尽量使用英语，如果英语水平不好，可以选择在中文论坛提问。）

### 已知的问题

* Currently the installer does not validate input correctly; if you enter an invalid admin username, the installer will silently fail. Usernames must only contain letters, numbers, dashes, and underscores.
* The installer will crash if the `flarum/storage/framework/views` directory is not writable. Make sure PHP can write to this directory.

## 调试

If you encounter an error while using your forum, the first thing to do is enable debug mode. Simply open up config.php with a text editor, and change the `debug` value to `true`. This will cause Flarum to show detailed error messages, giving you an insight into what's going wrong.

If you're still getting a blank page, try setting the `display_errors` option to `On` in your php.ini configuration. Alternatively, you can add the following line of code to the top of `flarum/bootstrap.php`:

```php
ini_set('display_errors', 'On');
```

*Be sure to revert these changes once the error is fixed!*
