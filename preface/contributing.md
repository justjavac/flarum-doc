# 贡献

## 报告漏洞

如果你发现了一个漏洞，我们愿闻其详。请记住，漏洞报告的目标是使我们能轻松地重现此漏洞，并开发出对应补丁。因此，你应该提供尽可能多的相关信息，并且用清晰且基于事实的语言与我们沟通。[这篇文章](http://www.chiark.greenend.org.uk/~sgtatham/bugs.html)是关于如何有效报告漏洞的绝佳材料。

如需报告漏洞，请在以下的 Github 库中创建问题（issue）：

- [**核心(Core)**](https://github.com/flarum/core)
- [BBCode](https://github.com/flarum/bbcode)
- [表情(Emoji)](https://github.com/flarum/emoji)
- [喜欢（赞）(Like)](https://github.com/flarum/likes)
- [锁(Lock)](https://github.com/flarum/lock) 
- [Markdown](https://github.com/flarum/markdown)
- [提到(Mentions)](https://github.com/flarum/mentions)
- [推送器(Pusher)](https://github.com/flarum/pusher)
- [置顶(Sticky)](https://github.com/flarum/sticky)
- [订阅(Subscriptions)](https://github.com/flarum/Subscriptions)
- [暂停(Suspend)](https://github.com/flarum/suspend)
- [标签(Tags)](https://github.com/flarum/tags)

## 贡献代码

想帮助我们构建 Flarum？真是太棒了！我们欢迎贡献，并以 Pull Requests 的方式接受。请在提交任何 Pull Requests 前先阅读这些指导。

查阅[路线图](https://github.com/issues?utf8=✓&q=is%3Aopen+is%3Aissue+user%3Aflarum+)来了解需要做些什么。为使人们能尽量轻松地开始参与贡献 Flarum，我们已对 Github 上的很多问题都做了详细的说明。这些说明包括了该做什么、去哪里做的指示。[来看看有没有什么你喜欢做的!](https://github.com/issues?utf8=✓&q=is%3Aopen+is%3Aissue+user%3Aflarum+)

### 开发环境

要开始贡献代码，你需要设置一个开发环境，最简单的方法就是安装 Flarum 的 Vagrant 镜像了：

1. 安装 [Vagrant](https://www.vagrantup.com) 和 [VirtualBox](https://www.virtualbox.org)；
2. Clone Github 上的 flarum/flarum 库并且在 Vagrant box 中安装:

```bash
git clone --recursive https://github.com/flarum/flarum.git
cd flarum
vagrant up
```

3. 在你的 `/etc/hosts` 文件中添加：`192.168.29.29 flarum.dev`；
4. 在浏览器中浏览 flarum.dev。

如果你出于某种原因而不能使用 Vagrant，你依然可以在你的本地服务器上安装 Flarum。你将需要安装 [Composer](https://getcomposer.org/), [Node.js](https://nodejs.org/) 和 [Gulp](http://gulpjs.com/)。Clone flarum/flarum 库然后运行[相关命令](https://github.com/flarum/flarum/blob/master/flarum/vagrant/environment.sh)来安装依赖项。

一旦你安装了核心，你可能也需要安装一些与 Flarum 捆绑的扩展。Clone 每个[扩展库](https://github.com/flarum) 到你的 `extensions` 文件夹下, 并按照提供的说明安装依赖项并编译对应资源。（一般而言，这意味着需要在根目录下运行 `composer install'，然后在 `js/admin` 和 `js/forum` 文件夹下运行 `npm install`。）

### Pull Request 指南

* 若想提交包括历史的 commit，请确保在你 pull request 中的每个 commit 都具有相对独立的意义；如果你在开发过程中不得不多次提交了中间版本，请在提交之前进行[squash](http://git-scm.com/book/en/Git-Tools-Rewriting-History) 操作。
* 你也可能需要进行 [rebase](http://git-scm.com/book/en/Git-Branching-Rebasing) 操作以避免合并冲突。

### 测试

关于 Flarum 测试部分的故事仍在书写；如果你有测试相关经验，请[帮助我们开发可靠的测试策略](https://github.com/flarum/core/issues/)！

### 样式与约定

代码一致性非常重要！请确保你书写的代码符合以下编码风格和命名约定。

PHP 代码应遵循 [PSR-2](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md) 编码标准和 [PSR-4](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-4-autoloader.md) 自动加载标准。

JavaScript 代码应粗略地遵循[Airbnb Style Guide](https://github.com/airbnb/javascript)。将有一个 `.eslintrc` 文件可用来检查你的贡献。

*TODO: CSS style/naming conventions, database columns, translation keys*

> 译者：[@ttnl](https://github.com/ttnl)  
