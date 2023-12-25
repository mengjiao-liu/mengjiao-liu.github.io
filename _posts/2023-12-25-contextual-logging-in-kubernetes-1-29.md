---
layout: post
read_time: true
show_date: true
title: "Kubernetes 1.29 中的上下文日志记录：更好的故障排除和增强的日志记录"
date: 2023-12-25
img: posts/20231225/log.png
tags: [kubernetes]
author: Mengjiao Liu
description: "增强 Kubernetes 可观察性"
---

# Kubernetes 1.29 中的上下文日志记录：更好的故障排除和增强的日志记录

[KEP-3077](https://kep.k8s.io/3077)：kube-scheduler 已转换为上下文日志记录（Alpha）

Kubernetes v1.24 中引入的上下文日志记录功能现已成功迁移到两个组件（kube-scheduler 和 kube-controller-manager）以及一些目录。
该功能旨在为 Kubernetes 提供更多有用的日志以更好地进行问题追踪、故障排除。目前该功能处于 Alpha 阶段，如需使用请启用 `ContextualLogging` 特性门控。

示例：
```
I1113 08:43:37.029524 87144 default_binder.go:53] "Attempting to bind pod to node" logger="Bind.DefaultBinder" pod="kube-system/coredns-69cbfb9798-ms4pq" node="127.0.0.1"
```

更详细的大家可以直接阅读 Kubernetes 官方博客[Contextual logging in Kubernetes 1.29: Better troubleshooting and enhanced logging](https://kubernetes.io/blog/2023/12/20/contextual-logging-in-kubernetes-1-29/)，这篇博客也是由我撰写。
