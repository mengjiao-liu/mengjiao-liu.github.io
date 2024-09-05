---
layout: post
read_time: true
show_date: true
title: "想成为未来的 Kubernetes 发布团队负责人吗？作为 1.29 Release Shadow 的一点小小体悟"
date: 2024-09-05
img: posts/20240905/release-shadow.png
tags: [kubernetes]
author: Mengjiao Liu
description: "Kubernetes Release Team Shadow 感悟"
---
**这篇博客首发位于 https://mp.weixin.qq.com/s/guNCodwaZT9s179TJDuxMw。**
# Release shadow 是什么？

> The Release Team recruits a number of apprentices for each release role, in order to train new release team leads, handle tasks that each lead may not be able to cover, share knowledge about the release process, and help contributors broaden their areas of knowledge and participation. These apprentices are called "Shadows" on the release team, and are equivalent to similar trainee positions on other teams, such as the Release Management Assistants.
> 

简单来说，你可以将 Release shadow 视为一种培训制度，在每个 Release 角色负责人的指导下完成一些工作，在此期间，你会了解到 Kubernetes 发布的流程，拓宽有关 Kubernetes 的知识和参与的领域。当你持续经历几个版本后，之后你就可以胜任为新的发布团队负责人。

# 为什么要申请？

现在你已经知道 Release shadow 是做什么的了，那你可能会问，“为什么我要申请呢？”

答案是，好处多多：

如果你是 Kubernetes 用户：

你可以在发布周期就能密切关注此版本有哪些功能新增、更新以及移除。可以更稳健地使用和升级 Kubernetes。

如果你是 Kubernetes 开发者：

Kubernetes 本身就非常复杂，拥有很多组件，Release shadow 是一个很好的桥梁，参与发布流程能让你深入社区、SIG、WorkGroup 等，可以更好地参与Kubernetes 社区，与社区维护者进行沟通，并做出更多的贡献。

如果你想发展其他的开源项目：

Kubernetes 作为一个大型开源项目，其发布流程也是非常复杂的。只有你参与其中，才能更好地了解这是怎么运转的，对你之后稳健运行其他开源项目是非常有帮助的。

# 申请过程

申请过程非常的简单，你只需要填写一份 Google 申请表单（即将到来的 [1.32 shadow 申请](https://docs.google.com/forms/d/e/1FAIpQLSdb60FW9aYIepSdXIWexQIKNJ8m3JSqHZ6kkH3Q_I7XP9OVYA/viewform)在此）即可。

只需要注意的是，Release shadow 有不同的角色，分别是：

1. [bug-triage &](https://github.com/kubernetes/sig-release/tree/master/release-team/role-handbooks/bug-triage) [ci-signal](https://github.com/kubernetes/sig-release/tree/master/release-team/role-handbooks/ci-signal) （这两个角色从 1.30 起合并）
2. [communications](https://github.com/kubernetes/sig-release/tree/master/release-team/role-handbooks/communications)
3. [enhancements](https://github.com/kubernetes/sig-release/tree/master/release-team/role-handbooks/enhancements)
4. [release-notes](https://github.com/kubernetes/sig-release/tree/master/release-team/role-handbooks/release-notes)
5. [docs](https://github.com/kubernetes/sig-release/tree/master/release-team/role-handbooks/docs)

你申请之前最好看下 Release shadow 角色[手册](https://github.com/kubernetes/sig-release/blob/master/release-team/role-handbooks)，选择一个你感兴趣的方向以及你觉得你能胜任的角色。超级简单！

我在 Kubernetes 1.29 发布周期申请的时候，是申请的 Release Notes 这个 shadow 角色。 因为我当时正好想了解下 1.29 版本的新功能，加上我此前一直为 SIG Docs 贡献和审阅 PR。所以这个方向我比较感兴趣，也能很好地胜任。

千万不要有太多顾虑，Just do it！ 社区对新人是非常友好的~

当你提交完申请后，接下去做的就是等待……

然后，某一天你会在你的邮件里发现你被通知选上 Release shadow 啦~

当然，不会是无休止的等待啦！ 在你提交申请的时候，就会告诉你 shadow 通知的时间，你只需要在那两天关注一下就可以啦~ 

为什么是两天，因为有时区差异，so bad。

如果落选，Release 团队同样会发一封邮件，告诉你非常遗憾未能选上。

之前我的申请也落选过两次，第三次我怀疑是我的介绍写的更详细，选的方向也更加有针对性才被选上的。总之，试试呗，万一成功了呢~

# 具体经历

我当时在入选了 Release note shadow 后，release 团队在邮件里就告知了我的角色负责人是谁。以及他会在未来一周内聚集我们这些shadow。

当时我的角色负责人是 fsmunoz，他在 slack 上将我们添加到一个 1.29 Release note shadow 组中。然后，发送给我们一些  shadow 要做什么，怎么做的指南，让我们先看一下，了解流程。

Kubernetes 每个版本发布是有对应的发布周期的，基本上一开始 release 团队负责人们就会制定大致的时间表。Kubernetes 将按照计划进行新功能提交冻结、构建、测试、发布等。

当然，这些作为 shadow只需要大致了解就行，理论加实践，才是最快的学习方法。

每周都会有 Release team 例会，shadow 最好全部参加以了解最新进度。但是不是必须的。Shadow 会被分配汇报任务，一般每个 shadow 会轮流，基本上，可能一个月会轮到一次。轮到你的这次记得出席会议。

Release team 例会会有对应的共享文档，开会之前你需要把你需要汇报的内容更新到对应的那一周的角色模版里。开会的时候主持人会一项项过，检查哪些点被阻塞了，以及哪些地方是正常运行的。

轮到你的时候，你要简要概括下你们角色团队最近一周的进度。如果你对此感到紧张，别担心，很多人都会这样，这很正常，我也是这样过来的。但参加的越多，你会越从容，不要怕，说出你做的事情，不要担心英语发音和语法问题，没有人会嘲笑你。至少我没有遇到过。

时间就这样悄悄地流逝……

到了最关键的 Kubernetes 版本发布节点，你会发现也许发布团队会遇到构建失败问题、某个 PR 导致测试不过，以及，正好这时发现了重大 bug…… 所有人都在为此忙绿，Kubernetes 社区也是因为每次有发布团队的贡献才能一次次地稳定发布。作为 shadow，做你能做的就好了，就是在为社区尽自己的一份力，发布里程碑的奖章上也有你的一份！

# 感悟

经历了 Release shadow 这个角色后，我更加了解了在 Kubernetes 这个庞然大物后面，除了开发者，还有很多人在为它的运行贡献者自己的力量！也正是因为有 release shadow 这样的机制让社区能源源不断地涌入新的贡献者。传承，是开源项目能够发展壮大的重中之重。也因此，Kubernetes 走过了十年，正在迈向第二个十年！ 这也是很多国内开源项目要学习的地方。

与我而言，在参与 Release shadow 的过程中，能够跟全世界的开源贡献者一直交流，学习和进步，就是非常大的收获。尽管我们时区不同、国家不同，但是都为了同一个目标而努力。

文章到这里就要结束啦~ 希望之后能看到更多国内 shadow 的分享~

