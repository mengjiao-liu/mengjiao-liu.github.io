---
layout: post
read_time: true
show_date: true
title: "邮件平台 mailchimp"
date: 2021-09-13
img: posts/20210913/email.png
tags: [mail]
author: Mengjiao Liu
description: "博客评论平台 disqus"
---
## 背景
我在 [GitHub Pages](https://pages.github.com/) 上搭建了自己的博客网站，搭建过程中用到了邮件订阅这个功能，

使用邮件订阅可以让订阅者继续关注你未来的文章，可以提高网站留存率。

我使用的主题使用的邮件发送平台是 [mailchimp](https://mailchimp.com/).

但 mailchimp 这个平台 UI 界面实在令人头大，太难找到对应的功能啦！！

我花了很多时间在这个配置界面，
看了 youtube 视频（吐槽一句：官网视频 youtube 也不是最新的界面）和他们的官网文档，最后才找到。

以前版本，mailchimp 的收件人列表叫 `Lists`,现在叫 `Audiences`，
而且以前是横向排列的 ![横向排列](assets/img/posts/20210913/old_lists.png)

现在改成竖式的菜单选项，差别还是挺大的。
![new_menu](assets/img/posts/20210913/new_menu.png)

## 目标
我的主题要求我添加一个来自 Mailchimp 的订阅列表链接。
示例如下
```yaml
"https://gmail.us5.list-manage.com/subscribe/post?u=9fbb8b48e6b41210766fb9506&id=93100d6d9b"
```


未完待续...


