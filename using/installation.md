# 安装

## Beta 版软件

请留意 Flarum 正处在 Beta 版软件阶段。这意味着：
- 它还有很多**没完成的功能**和 **Bug**， 🐛🐞 而且
- 某些时候 —— 可能早可能晚 —— 它甚至会**崩溃**！ 💥

Beta 阶段关乎解决这些问题并改善 Flarum。我们正在努力使 Flarum 更好，所以我们希望你：
- **不要在生产环境中使用它**。如果出错了，我们可帮不了你。况且升级到未来的版本可能是个脏累活儿。
- **有责任感地汇报 Bug**。书写差劲的 Bug 报告需要花很多时间处理，会扰乱我们添加新功能或者使 Flarum 更稳定的进程。
在你安装前，请阅读我们的[贡献](contribution.md)规则，你将了解你注册的意义！

## 系统需求
> 注意：仅就 0.1.0-beta.3 版中，Flarum 使用 [Composer](https://getcomposer.org/) 管理依存关系和扩展。这意味着 **Flarum 没法在没有 SSH（命令行）连接的主机上安装**。我们未来会保证 Flarum 能用其他的方法安装，使之可被所有人使用。与此同时，如果现在你希望运行 Flarum，你需要寻找允许 SSH 连接的主机。

下为运行 Flarum 所需的一些条件。
- 网页服务器软件： **Apache** (需要支持 mod_rewrite)，**Nginx**，或者**Lighttpd**
- **PHP 5.5**+，需要开启的扩展：mbstring, pdo_mysql, openssl, json, gd, dom, fileinfo
- MySQL 5.5+
- SSH（命令行）连接

## 安装 Flarum

Flarum 使用 Composer 做为管理依存和扩展的工具。所以，在安装 Flarum 之前，你需要在你的电脑上安装 [Composer](https://getcomposer.org/)。然后在 Flarum 应该安装的地方运行此命令：
```shell
composer create-project flarum/flarum . --stability=beta
```

在此命令运行时，你就可以为你的服务器配置 URL 重写了。最后，使用浏览器访问你的论坛，完成最后的安装步骤。

## URL 重写

### Apache

Flarum 附带了一个 Apache 的 .htaccess 文件 —— 记得正确地上传它。如果你在使用共享空间，确保你的空间提供商开启了 `mod_rewrite`。你也可能需要将下述加入到你的 Apache 配置中：
```xml
<Directory "/path/to/your/forum">
    AllowOverride All
</Directory>
```

### Nginx

添加下述配置到你的服务器配置块：
```
    location / { try_files $uri $uri/ /index.php?$query_string; }
    location /api { try_files $uri $uri/ /api.php?$query_string; }
    location /admin { try_files $uri $uri/ /admin.php?$query_string; }

    location /flarum {
        deny all;
        return 404;
    }

    location ~* \.php$ {
        fastcgi_split_path_info ^(.+.php)(/.+)$;
        fastcgi_pass unix:/var/run/php5-fpm.sock;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param HTTP_PROXY ""; # Fix for https://httpoxy.org/ vulnerability
        fastcgi_index index.php;
    }
    
    location ~* \.html$ {
        expires -1;
    }

    location ~* \.(css|js|gif|jpe?g|png)$ {
        expires 1M;
        add_header Pragma public;
        add_header Cache-Control "public, must-revalidate, proxy-revalidate";
    }

    gzip on;
    gzip_http_version 1.1;
    gzip_vary on;
    gzip_comp_level 6;
    gzip_proxied any;
    gzip_types application/atom+xml
               application/javascript
               application/json
               application/vnd.ms-fontobject
               application/x-font-ttf
               application/x-web-app-manifest+json
               application/xhtml+xml
               application/xml
               font/opentype
               image/svg+xml
               image/x-icon
               text/css
               text/plain
               text/xml;
    gzip_buffers 16 8k;
    gzip_disable "MSIE [1-6]\.(?!.*SV1)";
```

### Lighttpd

添加下述配置到你的服务器配置块：
```
url.rewrite-if-not-file = (
    "/admin.*" => "/admin.php",
    "/api.*"   => "/api.php",
    "/.*"      => "/index.php"
)
```
## 故障排除

如果你在安装 Flarum 中遇到问题，记得检查支持论坛的 [Installation 标签](http://discuss.flarum.org/t/installation) —— 也许有人和你遇到了相同的问题！如果没有，发起讨论，我们会尽可能帮你解决。

## SMTP

目前还没有配置 SMTP 的图形化界面 ([#258](https://github.com/flarum/core/issues/258))，但您可以手动修改数据库的 `config` 表：

```
mail_driver: smtp
mail_host: ...
mail_port: ...
mail_username: ...
mail_password: ...
mail_encryption: ...
```

## 导入数据

最终我们希望可以将其他论坛的数据导入到 Flarum 中，这样你就可以放心地迁移到 Flarum 软件。 然而，在这个阶段还为时过早，我们需要先发布稳定版！

> 译者：[@justjavac](https://github.com/justjavac)
> Updated by ttnl 17/1/21
