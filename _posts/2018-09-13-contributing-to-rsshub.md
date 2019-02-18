---
key: 20180913
modify_date: 2019-02-18
tags: [Node, RSS, 中文]
title: 向开源致敬：RSSHub - 万物皆可 RSS
cover: /assets/images/2018-09-12/rsshub-contribution.png
---

Henry 同志现已加入 RSSHub 豪华贡献者大军。

> RSSHub 是一个轻量、易于扩展的 RSS 生成器，可以给任何奇奇怪怪的内容生成 RSS 订阅源。

<!--more-->

---

# RSSHub 简介

自从开始学 Node.js 以来，一直在自己钻研，决定找一个开源项目练手。

[RSSHub](https://github.com/DIYgod/RSSHub){:target="_blank"}，一个正确的项目，在正确的时间，出现在了正确的地方 -- [GitHub Explore](https://github.com/explore){:target="_blank"}！

项目作者 [DIYgod](https://github.com/DIYgod){:target="_blank"} 小姐姐于2018年4月8日产下了这枚项目，旨在「给任何奇奇怪怪的内容生成 RSS 订阅源」。

# 理清项目脉络

## 项目架构

项目采用了 [koa](https://koajs.com/){:target="_blank"} 框架，同样来自之前自学的 Express 团队的作品。相较于的 Express，koa 更为轻量级，只包含了极为少量的中间件内核，可以看作是对 Node.js 的 HTTP modules 做了一个抽象化的实现。

## 基本逻辑

> ~~万事万物都是接口。 -- 弗洛伊德~~

一定程度上来讲，RSSHub 是一个 API 服务器，通过 `axios` 和 `cheerio` 组合（少量路由也借助了 `puppeteer`），抓取路由内定义的网站内容，返回的结果是则是通过由国人开发的 [art-template](https://aui.github.io/art-template/){:target="_blank"} 进行处理，生成符合 `RSS 2.0`，`Atom` 或 `JSON Feed` 格式的结果。

## 其他琐碎但重要的东西

- 支持通过内存或 Redis 对结果做缓存，防止重复请求源网站，消耗服务器资源的同时也容易触发源网站的反爬虫机制。

- 支持多种部署方式：
  - 提供 Heroku 一键部署
  - 提供 Dockerfile
  - 提供 docker-compose
  - 提供 Google App Engine 部署教程

- 使用 [Vuepress](https://vuepress.vuejs.org/){:target="_blank"} 生成的文档站。

- 使用 ESLint 和 Prettier 规范代码格式，并通过 CI 对所有 PR 进行自动化审核。

- 使用 [Sentry](https://sentry.io/){:target="_blank"} 进行错误日志集中管理。

以上虽然是些琐碎的东西，但在我自己自学的过程中是没有考虑到或没有条件进行实践的。

# 贡献

在花了几天理解了项目的代码后，8月7日我提交了第一个 PR，截止至9月12日，我共计提交了71个 PR，为 RSSHub 添加了：

1. 添加了大量的实用路由，如：
    - [App Store 内购降价](https://docs.rsshub.app/#app-store-mac-app-store){:target="_blank"}
    - [谷歌学术关键词更新](https://docs.rsshub.app/#%E8%B0%B7%E6%AD%8C%E5%AD%A6%E6%9C%AF%E5%85%B3%E9%94%AE%E8%AF%8D%E6%9B%B4%E6%96%B0){:target="_blank"}
    - [中国驻外使领馆通知](https://docs.rsshub.app/#%E4%B8%AD%E5%9B%BD%E9%A9%BB%E5%A4%96%E4%BD%BF%E9%A2%86%E9%A6%86%E9%80%9A%E7%9F%A5){:target="_blank"}
    - [Hopper 特价机票](https://docs.rsshub.app/#hopper-flight-deals){:target="_blank"}
    - [大学公告通知](https://docs.rsshub.app/#%E5%A4%A7%E5%AD%A6%E9%80%9A%E7%9F%A5){:target="_blank"}
    - [GitHub Issue, Followers, Stars](https://docs.rsshub.app/#%E4%BB%93%E5%BA%93-issue){:target="_blank"}

1. 添加了路由的 HTTP Basic 认证，部分 RSS 阅读器支持该认证协议。
1. 添加了未来实现 API 接口的基础，以支持第三方项目。
1. 修改了部分现有代码，使其符合 ESLint 严格的要求。
1. 添加了英文文档。

# 收获

- 上手了一个真实服务于社群的开源项目，体验了开源社区的氛围，与公司内真的是千差万别，少了几丝谨慎规划的拘束，多了几分对前沿技术的拥抱。

- 练习了 koa 框架，大量的 npm packages，以及加深了对 Node.js 的中间件处理流程的理解。

- 在这些之上，我也收获了很多新的 RSS 订阅源，成功将对 app 推送通知的依赖再一次减少了许多。
