---
layout: post
key: 20180309
modify_date: 2018-03-14
tags: [Node, Let's Encrypt, RSS, Nginx, Docker, 中文]
title: 记录学习使用 Node 搭建 API 服务器的路程
---

很早就在拜读[湾区日报]( [https://wanqu.co/b/7/2015-05-24-behind-the-scenes.html](https://wanqu.co/b/7/2015-05-24-behind-the-scenes.html) )，觉得分享的内容与点评都很赞，而且这个分享模式非常简单高效。但对于我来说，唯一缺点是原文不是全文输出，需要跳转到外部继续阅读，打破了在一个 app 里阅读所有内容的习惯。 现在我的另一半也被我安利上了用 RSS 刷新闻，决定借此机会打造一个类似的服务，用于分享新闻。

![Mindmap](/assets/images/2018-03-09/mindmap.png)

<!--more-->

# Introduction
### Background
作为重度 RSS 用户，由于 Google Reader 的关闭，不得不转向了自建 [TTRSS]( [https://tt-rss.org/](https://tt-rss.org/) ) 的方案, 同时已经围绕着 RSS 阅读这个需求，陆续学习并搭建了不少服务：
* 学习了基本 Linux 命令
* Let’s Encrypt 部署全站 https
* 学习了 apache（已弃用）与 nginx 的基础运用（多域名部署，反向代理，load balancing）
* [Huginn]( [https://github.com/huginn/huginn](https://github.com/huginn/huginn) ) 内容提取（爬虫）中枢
* [Beehive]( [https://github.com/muesli/beehive](https://github.com/muesli/beehive) )，一个简化版的 [Huginn]( [https://github.com/huginn/huginn](https://github.com/huginn/huginn) ) ，但由于功能性逐渐没有办法满足需求以及不活跃的开发社区，所以弃用
* 一个简单的基于 [mercury parser]( [https://mercury.postlight.com/web-parser/](https://mercury.postlight.com/web-parser/) ) 的 TTRSS 全文获取[插件]( [https://github.com/WangQiru/mercury_fulltext](https://github.com/WangQiru/mercury_fulltext) )
* 为了在 RSS 中阅读微信公众号， 搭建了 [PhantomJS]( [http://phantomjs.org/](http://phantomjs.org/) )
* 大幅增加 Docker 的应用场景，减轻【服务器迁移】以及【版本迭代部署与测试】时的 workload

***终于基本实现了在一个 app 中阅读所有资讯的目标。***

### Target
1. 学习使用 Node 的各种包搭建 API 服务器
2. 学习 Node 下的单元测试
3. 抓取分享链接的全文，通过 Huginn 输出 RSS
4. TBA

# Technology
### Framework 
Node express 号称可以在 10 分钟内搭建出一个 RESTful API server，选择了 express 而不是熟悉的 .NET MVC，则是因为：

1. 借此机会学习 Node
2. js 当之无愧的最流行语言，正所谓第二第三加起来都没我多 [^1]
3. 650,000 已发布的包，日下载量近 1 亿 [^2]
4. ~~C# 已经写吐了~~

### Database
1. 重复使用已部署的 PostgreSQL，高可靠性与数据完整性你值得拥有
2. 重复使用已部署的数据库备份逻辑

### Testing & Integration
1. 暂定学习 [mocha](https://mochajs.org/) 和 [chai](http://chaijs.com/)
2. Travis CI

### Deployment
Docker 毫不犹豫。

Production 版本可以加入现有的 compose，做到 VPS 拎包入住，打包即走。

{% highlight yml %}
version: "3"  
services:
    postgres:
      image: sameersbn/postgresql:latest

    ttrss:
      image: wangqiru/ttrss

    huginn:
      image: huginn/huginn:latest

    aria2:
      image: wangqiru/aria2-with-ariang:latest

    filemanager:
      image: hacdias/filemanager:latest

    squid:
      image: sameersbn/squid:3.3.8-23
{% endhighlight %}

部署过程中遇到了 npm `phantomjs-prebuilt` package 无法在 alpine 下编译的问题，[解决方法]({% post_url 2018-03-13-phantomjs-for-npm-on-alpine %})。

### Documentation
曾有使用 [jsdoc](http://usejsdoc.org/) 的经验，等到某一个稳定的版本后开始增加文档。



##### 注
[^1]:  [https://octoverse.github.com/](https://octoverse.github.com/) 
[^2]:  [https://www.npmjs.com/](https://www.npmjs.com/) 
