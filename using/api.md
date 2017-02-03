# 使用 API

每个 Flarum 论坛都有一个公开的 JSON API，可以对论坛数据进行读和写。它符合[JSON-API 1.0 规范](http://jsonapi.org.cn/)。

## 身份验证

API 使用基于令牌的身份验证机制。某些终端不需要身份验证。您可以通过 `/api/token` 取得一个令牌:

    POST /api/token HTTP/1.1

    {
        "identification": "Toby",
        "password": "pass7word"
    }
    
    HTTP/1.1 200 OK
    
    {
        "token": "YACub2KLfe8mfmHPcUKtt6t2SMJOGPXnZbqhc3nX",
        "userId": "1"
    }

然后，您可以在后续的请求头中，包含此身份验证令牌:

    GET /api/forum HTTP/1.1
    Authentication: Token YACub2KLfe8mfmHPcUKtt6t2SMJOGPXnZbqhc3nX

## 论坛

* `GET /api/forum` - 关于论坛的信息，包括 groups 和 tags
* `PATCH /api/forum` - 更新论坛配置

## Discussion 讨论

* `GET /api/discussions` - 获取所有的讨论（默认排序规则为 `-time`）
    * `filter[q]` - 根据 username/gambits 过滤
* `POST /api/discussions` - 创建一个新的讨论
* `GET /api/discussions/:id` - 通过 ID 获取讨论
* `PATCH /api/discussions/:id` - 更新一个讨论
* `DELETE /api/discussions/:id` - 删除一个讨论

## Posts 帖子

* `GET /api/posts` - 获取所有帖子
    * `filter[discussion]` - 根据 discussion ID 过滤
    * `filter[user]` - 根据 user ID 过滤
    * `filter[number]` - 根据数字 (discussion 中的位置)
    * `filter[type]` - 根据 post type 过滤
* `POST /api/posts` - 创建一个新的帖子
* `GET /api/posts/:id` - 根据 ID 获取一个帖子
* `PATCH /api/posts/:id` - 更新一个帖子
* `DELETE /api/posts/:id` - 删除一个帖子

## Users 用户

* `GET /api/users` - 获取所有用户
    * `filter[q]` - 根据 username/gambits 过滤
* `POST /api/users` - 注册新用户
* `GET /api/users/:idOrUsername` - 根据 ID 和 username 获取一个用户
* `PATCH /api/users/:id` - 更新一个用户
* `DELETE /api/users/:id` - 删除一个用户
* `POST /api/users/:id/avatar` - 更新用户头像
* `DELETE /api/users/:id/avatar` - 删除用户头像

## Groups 组

* `GET /api/groups` - 获取所有组
* `POST /api/groups` - 创建一个新的组
* `PATCH /api/groups/:id` - 更新一个组
* `DELETE /api/groups/:id` - 删除一个组

## Notifications 通知

* `GET /api/notifications` - 获取所有通知
* `PATCH /api/notifications/:id` - 将一个通知标记为已读

## Tags 标签

* `POST /api/tags` - 创建一个新标签
* `PATCH /api/tags/:id` - 更新一个标签
* `DELETE /api/tags/:id` - 删除一个标签

> 译者：[@justjavac](https://github.com/justjavac)

<!-- 17/1/21 Checked by ttnl -->
