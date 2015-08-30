# 分发

分发的目标是使得别人能轻松的安装扩展，只需把它放到扩展文件夹里即可。他们可不应该需要装某种工具或者编译什么代码――那是你开发者的工作！

所以在你打包你的扩展之前，你需要安装好所有的 Compser 依赖项，并且编译你的 JavaScript 程序。一个打包脚本的示例：

```bash
#!/usr/bin/env bash

base=${PWD}

# Unzip an archive of the latest committed code
# 解压 Flarum 最终版
rm -rf /tmp/extension-release
mkdir /tmp/extension-release
git archive --format zip --worktree-attributes HEAD > /tmp/extension-release/release.zip
cd /tmp/extension-release
unzip release.zip -d ./
rm release.zip

# Install all Composer dependencies
# 安装 Composer 依赖
cd /tmp/extension-release/flarum
composer install --prefer-dist --optimize-autoloader --ignore-platform-reqs --no-dev

# Compile JavaScript
# 编译 JavaScript
# Assumes: npm install -g gulp flarum-gulp babel-core
cd /tmp/extension-release/js
for app in forum admin; do
  cd "/tmp/extension-release/js/${app}"
  npm link gulp flarum-gulp babel-core
  gulp --production
  rm -rf "/tmp/extension-release/js/${app}/node_modules"
done
```

> 译者：[@ttnl](https://github.com/ttnl)