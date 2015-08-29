# 安装

## 系统需求

- 服务器： Apache (需要支持 mod_rewrite) 、Nginx
- PHP 5.5+
- MySQL 5.5+

## 在线安装

1. 下载安装包，解压，上传文件到服务器
2. 访问域名根目录
3. 填写网址信息：如网站名称、管理员信息、数据库信息，点击“Install Flarum”按钮

## 命令行安装

1. 下载安装包，解压，上传文件到服务器
2. 进入根目录，运行 php flarum/flarum install.
3. 填写网址信息：如网站名称、管理员信息、数据库信息

## URL重写

在安装和运行Flarum之前，您需要设置URL重写规则。 Flarum附带了一个Apache的.htaccess文件，此文件包含了需要用到的规则。 如果您使用的是其他web服务器，您需要在配置文件中添加以下规则：

**Nginx**

```
location / {
    try_files $uri $uri/ /index.php?$query_string;
}

location /api {
    try_files $uri $uri/ /api.php?$query_string;
}

location /admin {
    try_files $uri $uri/ /admin.php?$query_string;
}

location /flarum {
    deny all;
    return 404;
}

location ~ .php$ {
    fastcgi_split_path_info ^(.+.php)(/.+)$;
    fastcgi_pass unix:/var/run/php5-fpm.sock;
    fastcgi_index index.php;
    include fastcgi_params;
}
```

## SMTP

目前还没有配置SMTP的图形化界面 ([#258](https://github.com/flarum/core/issues/258))，但您可以手动修改数据库的 `config` 表：

```
mail_driver: smtp
mail_host: ...
mail_port: ...
mail_username: ...
mail_password: ...
mail_encryption: ...
```

## 导入数据

最终我们希望可以将其他论坛的数据导入到 Flarum中，这样你就可以放心地迁移到Flarum软件。 然而，在这个阶段还为时过早，我们需要先发布稳定版！