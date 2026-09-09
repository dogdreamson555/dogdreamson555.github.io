---
title: "如何使用文章评论"
date: 2026-09-08T00:00:00+08:00
slug: comments-guide
draft: false
description: "如何使用文章下方的 GitHub 评论区，以及如何找到对应讨论。"
---

博客使用 giscus 接入 GitHub Discussions，每篇文章按固定路径关联自己的讨论。

## 阅读与发表评论

无需登录即可阅读文章和已有评论。发表评论时，点击评论区的 GitHub 登录按钮，按提示完成 giscus 授权，再输入评论。

提交后可以刷新页面，检查评论是否保留。也可以通过评论区的讨论链接前往 GitHub 查看；博客的讨论集中在[仓库 Discussions](https://github.com/dogdreamson555/dogdreamson555.github.io/discussions)。

## 不同文章的讨论

这篇说明的路径是 `/p/comments-guide/`；[工程预览示例]({{< relref "post/build-preview" >}}) 使用 `/p/build-preview/`。两篇文章应各自关联独立讨论，不共享评论。

文章改标题时会保持路径不变，因此原有评论仍关联原文章。首次没有人评论时，可能尚未创建对应的 GitHub 讨论。

## 评论区未加载时

正文独立于评论服务，可以继续阅读。如果评论区加载失败，可以稍后重试，或通过上方链接前往 GitHub Discussions。
