# FAQ

## 我有一个想法或者希望论坛实现的功能。我该怎么做？

在着手做任何事之前，我们希望您能够先检查以下几个地方，看看你的想法是否已经被提到：

* 浏览 [Features](http://flarum.org/features/) 页面，以知悉我们已推出的功能和即将推出的功能；
* 浏览 [Roadmap](https://github.com/flarum/core/issues/74) 页面，以了解我们正计划实施些什么；
* 在 Github 上搜索 [issues](https://github.com/issues?utf8=%E2%9C%93&q=is%3Aopen+is%3Aissue+user%3Aflarum+)，以确定你的想法是否已经纳入开发计划；
* 在[论坛](http://discuss.flarum.org)上搜索，看看你的想法有没有已经被讨论过。

如果你什么都没有找到，那么你下一步要做的是了解你功能的本质是否适合 Flarum 的核心。阅读[核心、捆绑的扩展和第三方扩展之间的区别](../extend/introduction.md)。这个功能是不是大多数论坛所需要的？亦或只是更适合作为一个第三方扩展？

如果你坚信它应该是核心的一部分，或者该做为捆绑的扩展，又或你并不确定，来[论坛](https://discuss.flarum.org/t/features)进行讨论！


## 运行 Flarum 需要怎样的服务器规格？

只要你的面包机有至少两个插槽还能插到墙壁上的插座上，Flarum 工作起来大概就没什么问题。

但是话说回来，Flarum 需要 **PHP 版本 5.5 以上**，**MySQL 也必须是 5.x 的版本**（注：另有文档称 MySQL 版本亦需为 5.5 以上）；同时需要 GD 和 Imagick 扩展来处理头像。我们已经在 Apache 和 Nginx 服务器上测试了 Flarum，它应该在任何共享空间或者 VPS 上都工作的很好――请在[官方论坛](http://discuss.flarum.org)让我们了解您的体验！

## Flarum 要花多少钱？

Flarum 一分钱也不要。Flarum 百分百开源，百分百免费，而且永将如此。真的，永将如此。

然而，我们也接受捐款。我们并不会规定如何使用这个软件，但如果你想以财务捐赠的方式来表示你对本软件的支持的话，你可以在此了解[捐款信息](http://flarum.org/donate/)。捐款将用于支付托管所需成本和专用的开发时间。

## 我能把我的论坛迁移到 Flarum 么？

可以，不过尚需等待。我们现在的重点是让 Flarum 更加稳定，并且功能完备。一旦我们已有将 Flarum 应用于生产环境的信心，我们就会制作导入其他论坛数据的工具，比如 esoTalk，FluxBB，phpBB，等等。

## 什么时候发布 Flarum 正式版？

我们也不能保证什么时候可以发布——因为中间的变数太多！不过，你可以按照 [Roadmap](https://github.com/flarum/core/issues/74) 关注我们的开发进度。

## 为什么我不能排版我的帖子？

Flarum 目前暂无可视的编辑器――因为我们在 Beta 版之前还没有准备好。请放心，这已被纳入计划，很快就将在核心中出现。

尽管现在还没有可视编辑器，Flarum 已支持 Markdown 和 BBcode 语法。Markdown 是一种用于表示电子邮件和新闻组帖子的，基于结构化文档的纯文本语法格式。如果你对 Markdown 毫不熟悉，请看看这个[快速入门](http://justjavac.com/jekyll/2012/03/31/markdown-basics.html)以帮助你快速在 Flarum 上排版。

## Flarum 会有其他语言的本地化么？

会的，这取决于社区的贡献。我们已经有一些优秀的会员，提供他们的专业知识。来帮助 Flarum 本地化的发展。如果对[本地化](../extend/localization.md)有兴趣，请看看本地化文档，也请[参与讨论](http://discuss.flarum.org/d/336-potential-l10n-stumbling-blocks)。

> 译者：[@ttnl](https://github.com/ttnl)
