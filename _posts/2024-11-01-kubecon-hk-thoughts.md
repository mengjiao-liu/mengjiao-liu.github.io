---
layout: post
read_time: true
show_date: true
title: "一位 Kubernetes 女性开发者的香港随笔"
date: 2024-11-01
img: posts/20241101/hk.png
tags: [kubernetes]
author: Mengjiao Liu
description: "去香港参加 Kubecon 有感"
---
**这篇博客首发位于 [https://mp.weixin.qq.com/s/V9jiY6kTjR-oHXMMTr4lJw](https://mp.weixin.qq.com/s/V9jiY6kTjR-oHXMMTr4lJw)**


算起来这次香港 KubeCon 是我第三次在线下参加 KubeCon 了。之前因为众所周知的原因是参加的线上的会议。线下参与会议确实更让人期待，可以和平时仅能在线上而且跨时区异步交流的贡献者们同步交流。

![开场](https://daocloud.feishu.cn/space/api/box/stream/download/asynccode/?code=NmE1NmRlZGY1ZmE1ZjI0NmMwNGNhMmQ5ZWUzMjJmYTVfWGJLZWM4TVQ0RU5RNDM0UWloSjF5ZldoV1VMb3BKY2xfVG9rZW46SGxBb2J4ekhLb2hyT3J4MVIxdGN4WlZIbmVkXzE3MzA0NTUyMDE6MTczMDQ1ODgwMV9WNA)


# 小而精的线下体验

先简单描述下这几场会议带给我的整体感觉：

去年上海的场地规模较大，各个会议房间隔得太远，要不停地上楼下楼，从这一层的一头走到另一头，简直堪比竞走。有时候会因为场地太大导致错过本来想听的会议😅。而人数又没有那么多，稍显冷清。

今年巴黎举办的 KubeCon 则是人数和场地比较相符，场地很大，人数过万，导致社区维护者们其实碰面的机会很少，并没有很多机会可以面对面进行交流。

而今年在香港举办的 Kubecon，人数和场地刚刚好。举办之前就得知这次是在酒店举办，只有几个会议房间，当时还觉得可能会很逼仄。但真的参与其中的时候，却在这一方天地中感受到了充满活力的社区力量。感谢 CNCF 和 本次 KubeCon co-chair 的组织工作，让此次会议非常活跃，也促成了维护者们和贡献者们、维护者之间的良好地交流。

![线下交流](https://daocloud.feishu.cn/space/api/box/stream/download/asynccode/?code=YzJkNmU3YTY3YzZmMDA3ZGFlNDQ4NWFmNmNkZGYzMmJfYnRuN0VDRnEyd09pTkROVW9QYVdHREhCdEFHbUtlMThfVG9rZW46RThFcmJGVDVvb1VuWkJ4eVB4b2NXMUtWblFkXzE3MzA0NTUyMDI6MTczMDQ1ODgwMl9WNA)

# Kubernetes 十年圆桌讨论

本次大会上，我作为 Speaker，和 Paco 一起组织演讲了 Kubernetes 维护者的圆桌讨论。这次圆桌讨论我们邀请了国内在 Kubernetes 社区的主要维护者。大家一起讨论了：自己在 Kubernetes 社区的经历，以及 Kubernetes 存在的问题，以及 Kubernetes 的未来等问题。

Kubernetes 诞生于十年前，当时容器技术还未普及。大家对于容器编排也没有很广泛的需求。

但是十年后的今天，凭借迅猛的发展，Kubernetes 已经成为了容器编排领域的事实标准。随着 CRI、CSI、CNI 等技术的不断涌现，大家可以在 github 上可以搜到更多适配或者服务 Kubernetes 的工具，Kubernetes 的生态系统也在不断壮大，尽管这过程中也伴随着一些问题和兼容性挑战。而现在，它已经趋于成熟，发展放缓，更加注重对于客户在生产层面的稳健使用。

社区的发展是需要很多人的协同来推动的。不同的厂商、不同的贡献者都会有不同的需求和视角，开放包容才能让Kubernetes 社区走到现在，这同样也是开源的魅力所在。

开发者也可以多多参与社区交流，比如这次 KubeCon 就是一个很好的了解不同视角的机会，如果线下无法前往，那么所有视频也会在线上发布，大家可以多多关注哦。

比如，如果你对我演讲的这次圆桌讨论感兴趣，详情大家可以去[看视频](https://sched.co/1eYca)了解哦。

![圆桌讨论](https://daocloud.feishu.cn/space/api/box/stream/download/asynccode/?code=N2IyZDg2YzBlODUxMTY2N2FmODVjYWRmMTdmNGY5NTBfUHZzeEp4T29pZDZzNmhHVkxvcDV5N0tnTVZFbXduUzFfVG9rZW46UXVIaWJobTE5b0cyZFR4RHQ4WmMwMTRjblZoXzE3MzA0NTUyMDI6MTczMDQ1ODgwMl9WNA)

# 女性赋能

在计算机的世界里，女性的占比一直不高。CNCF 社区和 Kubernetes 社区一直比较注重女性和非二元性别个体的参与，致力于打造多样化的环境。在线下的讨论中，确实听说过类似的说法，有很多公司还或多或少以性别看人。

在去年的上海 KubeCon 和这次的香港 KubeCon 都有女性赋能会这样一个 session，可以有单独的会议室让女性或非二元性别个体进行讨论。

这样的环境，确实让我认识了很多同样工作在社区中的其他女性或非二元个体。作为社区中的少数人，如何在社区中更好地贡献、成长以及如何能成为领导者，还有如何解决女性或非二元个体面对的困境等等问题，也得到了解答。

在这里我认识了很多朋友。回想起来，其中的一些人最初在上海的 KubeCon 上相遇，而这次在香港的重逢，这让我更觉欢喜。在这个充满活力的社区中，还有很多跟我同样的人同样在这个社区努力着……

希望女性或非二元个体不论在哪里都能受到平等的对待。

下图是当时的合照，当时我要去参加 Kubernetes 十年的圆桌讨论，没有来得及合影。

![女性赋能会合影](https://daocloud.feishu.cn/space/api/box/stream/download/asynccode/?code=ZjE0NDE0N2VkNDJiMDdjNmE2MTc4MjJkMGFlMzNkNzhfWjRCS2M1bkNYZGZPSjFXV0xEWVJHcjJaRjZ3ZGU3VWNfVG9rZW46UlRKWmJkcXlybzlkanp4eVh1UWNxRVJBbldjXzE3MzA0NTUyMDI6MTczMDQ1ODgwMl9WNA)

插曲：

在会议结束休息期间，我认识了一位来自美国的女性开发者，她会议结束后会在中国旅行一段时间。我们讨论了中国的美丽城市和汉服、古筝等，并且热烈推荐她来西安穿古装~（她给我们看了她的计划表，其中包含：北京、广东、桂林、成都等）。又一次感受到 China travel 确实挺火的，中国也在越来越吸引外国人的目光……

# 一点小小的遗憾

令人惊喜的是，这次会议请来了 Linus，终于见到真人了，但是很可惜没有达成合影成就。

![Linus 出场](https://daocloud.feishu.cn/space/api/box/stream/download/asynccode/?code=ZjNlYmVkNGFmYTg4NjUxOWRkOTZlMmQyZWE0ZDFiNjFfN2ttWno5ZWJydkpmS0puOUF0OW1taUlFc2xqQk5XSHNfVG9rZW46RVFaWmJBN0N6b3RtZm94Y1JPVmNEcFpnblNoXzE3MzA0NTUyMDI6MTczMDQ1ODgwMl9WNA)

PS：同伴们都拿到了就更羡慕了！还把他们的头像换成了和 Linus 的合影😂。

![同伴和 Linus 的合影](https://daocloud.feishu.cn/space/api/box/stream/download/asynccode/?code=NzdjMjk5NmI0N2EyYTU1YzkxNTQ2NDMxYjE1MjE1YjZfd1U1djVIVHNkbHJXeGJuNXN4M1UxdHBKTjNXakFnVmZfVG9rZW46SnNOR2IxcWhtb0F5Q1h4ejhuMGNCeXoybjhkXzE3MzA0NTUyMDI6MTczMDQ1ODgwMl9WNA)

希望下次有机会再合影吧。

另外，此次因为当时身体不太舒服没有去参加 Kevin 和鹏飞组织的 Maintainer Meetup，这是一场汇聚了 CNCF 众多项目维护者的讨论会议，希望未来有机会还能够参与其中，与各位维护者共同探讨和学习。

![维护者 meetup](https://daocloud.feishu.cn/space/api/box/stream/download/asynccode/?code=MGEzMTFiYzk4YzA5M2EyNDZlN2E4MDk5ODBmYmI2MmFfeWMycmI4REcxNnc5djBHUm9iWkdqYk9QUVQ5M2h1bVhfVG9rZW46UDhuNGJNMENEbzZPb0x4S0REbmM3WDh2blNiXzE3MzA0NTUyMDI6MTczMDQ1ODgwMl9WNA)

# 最后

这三天参加香港 KubeCon 的经历让我更加敢于和其他人去面对面交流，尽管我们来自不同的公司、说着不同的语言、来自不同的国家，但人与人之间的情感是共通的。即使有时我们的表达不够完美，这种直接的沟通方式仍然能够传递真挚的情感。这正是线上活动难以替代的体验，因为屏幕无法完全传递人与人之间的温暖和真诚。我想说的是，无论软件如何发展，它们背后的创造者和使用者都是人，使用软件的也始终是人。

最后，还是呼吁大家都来多参与开源社区，社区需要每一个人的力量~