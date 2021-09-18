---
layout: post
read_time: true
show_date: true
title: "邮件平台 mailchimp"
date: 2021-09-13
img: posts/20210913/email.png
tags: [mail]
author: Mengjiao Liu
description: "邮件发布平台 mailchimp，可以让订阅者继续关注你未来的文章，可以提高网站留存率。"
---
## 背景
我在 [GitHub Pages](https://pages.github.com/) 上搭建了自己的博客网站，搭建过程中用到了邮件订阅这个功能，
使用邮件订阅可以让订阅者继续关注你未来的文章，可以提高网站留存率。
我使用的主题使用的邮件发送平台是 [mailchimp](https://mailchimp.com/).
但 mailchimp 这个平台 UI 界面实在令人头大，太难找到对应的功能啦！！

我花了很多时间在这个配置界面， 看了 youtube 视频（吐槽一句：官网视频 youtube 也不是最新的界面）和他们的官网文档，最后才找到怎么操作。

以前版本的 mailchimp 的收件人列表叫 `Lists`,现在叫 `Audiences`，
而且以前是横向排列的 ![横向排列](assets/img/posts/20210913/old_lists.png)

现在改成竖式的菜单选项，差别还是挺大的。
![new_menu](assets/img/posts/20210913/new_menu.png)


## 目标
我的主题要求我添加一个来自 Mailchimp 的订阅列表链接。
示例如下
```yaml
"https://gmail.us5.list-manage.com/subscribe/post?u=9fbb8b48e6b41210766fb9506&id=93100d6d9b"
```

## 具体步骤

### set up
首先，我们需要创建一个账号

进入网站：https://mailchimp.com/，点击 `Get Started`
![get_start](assets/img/posts/20210913/get_start.png)

选择免费套餐，当然有需要也可以选择相应的套餐
![license](assets/img/posts/20210913/pay.png)

然后回答一些问题。
![answer](assets/img/posts/20210913/answer.png)

注册就成功了，可以开始操作了。
![set up](assets/img/posts/20210913/set_up.png)

### 得到用户订阅链接
选择 `Audience`。右侧面板有一个 `Manage Audience`。
![license](assets/img/posts/20210913/audience.png)

选择它，然后点击 `Signup forms`。
![answer](assets/img/posts/20210913/signup_forms.png)

选择第一个叫做  `Form builder` 的选项，点击 `select`。这样你可以收到订阅表单。用户点击网站的 `订阅` 按钮时就会看到这个页面。
![set up](assets/img/posts/20210913/form_builder.png)

你可以进行一些定制。Mailchimp 可以让你修改表单的布局，以及你希望用户填写的字段。这里建议表单字段尽可能简洁，不用太复杂，不然可能会让用户觉得麻烦。
![set up](assets/img/posts/20210913/form_builder_list.png)

要得到表单链接，只需复制 `Signup form URL` 中显示的短链接。
![set up](assets/img/posts/20210913/signup_form_url.png)

在浏览器中打开，短链接直接跳转成完整的 URL。这时你可以将完整的 URL `https://github.us5.list-manage.com/subscribe?u=9fbb8b48e6b41210766fb9506&id=93100d6d9b` 
复制到你的项目中，完成这个配置。
![set up](assets/img/posts/20210913/list_url.png)

完成啦 ~~~~ 



