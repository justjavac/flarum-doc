# Adding Languages

# 添加语言

It's easy to add a new language to your basic Flarum installation. Just follow the instructions below to download and install the language pack of your choice.

你可以很容易地添加一个新的语言到你安装的Flaum。只需要按照下面的说明来下载安装你选择的语言包。

Once you have added a language pack, you can [set it as the default language](#setting-the-default) for your forum. And if you ever find you don't need one of your installed language packs, you can always [disable it](#disabling).

你添加一个语言包之后，你可以[把它设置成你的论坛的默认语言](#setting-the-default)。不论何时你感觉不需要其中一个安装过的语言包了，你都可以[禁用它](#disabling)。

If you're using any third-party extensions, be sure to [read this](#third-party-extensions) before you start.

如果你正在使用任何第三方的扩展，一定要在开始动手之前[读这里](#third-party-extensions)。

## Language Pack Installation

## 语言包的安装

To begin, visit the [Extensions > Languages](http://discuss.flarum.org/t/languages) tag at the Flarum Community site and find a language pack that you want to install. Be sure to download the ZIP file for the Flarum version  you're running.

第一步，访问Flarum社区网站的[扩展 > 语言](http://discuss.flarum.org/t/languages)标签，并找到你想要安装的语言包。一定要确保下载的ZIP文件是符合你正在运行的Flarum版本的。

1. Unpack the ZIP file and read the instructions in the enclosed **readme.txt** file.
2. Log in to your server via SSH, FTP, or your provider's control panel.
3. Navigate to the **extensions/** directory of your Flarum installation.
4. Create a new subdirectory. *Name it exactly as directed in the instructions!*
5. Upload everything in the unpacked folder to the subdirectory you just created.
6. Use your browser to navigate to the **Extensions** page of the admin interface.
7. Locate the language pack you just installed and enable it.

1. 解压ZIP文件并阅读附上的**readme.txt**文件中的说明。
2. 通过SSH，FTP，或者你的服务提供商的控制面板来登录到你的服务器。
3. 跳转到你的Flarum安装的**extensions/**目录。
4. 创建一个新的子目录。*按照说明里指出的那样来命名！*
5. 上传解压出的文件夹里的所有东西到你刚刚创建的子目录。
6. 用浏览器跳转到管理界面的**Extensions**页面。
7. 选择你刚刚安装的语言包并启用它。

That's all there is to it! You should now be able to use the language selector in your site's header to switch your forum's display to the new language.

这就是你要做的所有的事！你现在应该已经能够使用网站的头部的语言选择器来把论坛切换到用新语言来显示了。

<a name="setting-the-default"></a>

## 设置默认语言

Once you have installed a language pack and made sure it's working, you may want to set it as the default language for new users and guests. You can do that on the **Basics** page of the admin interface.

安装一个语言包后请确认它在工作，你可能需要来把它设置成给新用户或者访客的默认的语言。你可以通过管理界面的**Basics**页面来完成。

<a name="disabling"></a>

## 禁用一个语言包

If you decide you don't need to support a certain language after all, you can turn it off. Simply locate the language pack in the **Extensions** page of the admin interface and disable it.

如果你最终决定停止支持一个特定的语言，你可以把它关掉。只需要在管理界面里定位到**Extensions**页面里的语言包并禁用它。

Disabling a language can useful if you're running a monolingual site and don't want the language selector to appear in the site header. The language selector is hidden when only one language is enabled.

当你的站点只支持一个语言而且你不希望网站的头部出现一个语言选择器，禁用掉一个语言是很有用的。当只启用了一个语言的时候，语言选择器是隐藏的。

<a name="third-party-extensions"></a>

## Third-Party Extensions

##第三方扩展

While language packs downloaded from the Flarum Community site will generally include translations for all the extensions that come bundled with Flarum, they *will not* as a rule cover any third-party extensions you may have installed. It is up to developers to provide and maintain translations for their extensions.

尽管从Flarum社区网站上下载的语言包会一般包括所有和Flarum捆绑的扩展的翻译，他们*不会*是一个在你安装的第三方扩展上也起作用的规则。由开发者来决定是否提供并维护他们的扩展的翻译。

So before you install a third-party extension, you should check to make sure it includes translations for each language pack you have installed. If you find that an extension doesn't support a language you need, please contact the developer directly and arrange to have the necessary translations added.

所以当你安装一个第三方扩展之前，你应该检查一下来确保它包含有适用于你所安装的所有语言包的翻译。如果你发现一个扩展不支持你需要的一个语言，请直接联系开发者并协商添加必要的翻译。

> 译者：[@imcaffrey](https://github.com/imcaffrey)
