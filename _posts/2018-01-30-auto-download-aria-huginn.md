---
key: 20180130
modify_date: 2018-01-30
tags: [Huginn, aria2, web automation, 中文]
title: 利用 Huginn 检测资源更新并触发 aria2 任务
---

利用 Huginn 检测资源更新并触发 aria2 任务，使用自动化解放双手。

<!--more-->

抛砖引玉：某些页面会定期发布资源更新，例如[ ubuntu 镜像](https://www.ubuntu.com/download/alternative-downloads){:target="_blank"} 或任意资源发布页面。利用 Huginn 的 Website Agent 和 Post Agent 我们可以定期检测资源发布页面，并获取下载链接通过 aria2 JSONRPC 来触发下载。

## Website Agent - 获取资源下载链接

### agent 参数 [^1]
![Huginn Website Agent](/assets/images/2018-01-30/huginn_agent_1.png){: .center-image} 

#### XPath selector/选择器
在 `extract:url:xpath` 中，我们运用 XPath 来获取下载链接的路径（**Huginn 同时支持 CSS 选择器**）。 XPath 是一种适用于 XML/HTML 的查询语句，可以用于抓取指定元素的属性，广泛运用于 Web 应用自动化测试和爬虫。

如果懒得学习 [XPath W3Schools 教程](https://www.w3schools.com/xml/xpath_intro.asp){:target="_blank"}，可以通过浏览器控制台获取 XPath 绝对路径，但有时候 Huginn 会无法识别浏览器自动抓取的 XPath 路径。

![浏览器控制台获取 XPath 路径](/assets/images/2018-01-30/xpath-selector.png){: .center-image} 


- 在两个 `xpath` 中填入 Chrome 返回的 ubuntu 镜像种子链接的 XPath 路径：**//*[@id="main-content"]/div[3]//div[1]/ul/li[1]/a**，结果为

{% highlight HTML %}
<a class="download-torrent" href="http://releases.ubuntu.com/17.10/ubuntu-17.10.1-desktop-amd64.iso.torrent">
  Ubuntu 17.10.1 Desktop (64-bit)
</a>
{% endhighlight %}

- 在 `url:value` 中填入 `@href`，意为抓取 XPath 所指向元素的 href 属性，结果为 **http://releases.ubuntu.com/17.10/ubuntu-17.10.1-desktop-amd64.iso.torrent**
- 在 `name:value` 中填入 `text()`，意为抓取 XPath 所指向元素的文本，结果为 **Ubuntu 17.10.1 Desktop (64-bit)** （ aria2 不需要这一属性也可以触发下载，因此可以省略）


### 输出事件

url|name
http://releases.ubuntu.com/17.10/ubuntu-17.10.1-desktop-amd64.iso.torrent | Ubuntu 17.10.1 Desktop (64-bit)


## Post Agent 1 - 发送下载链接至 aria2 触发下载

上一步输出事件包含了触发 aria2 下载任务所需的链接，在新建的 Post Agent 中，我们还需要输入：
- post_url：aria2 JSONRPC 地址
- token：aria2 JSONRPC 的令牌


### agent 参数
![Huginn Post Agent](/assets/images/2018-01-30/huginn_agent_2.png){: .center-image} 



### credentials 参数
在 huginn/user_credentials 中，用户可以填入密码，API key 等保密数据，然后通过 `{{ "{% credential <name> " }}%}` 在 agent 参数中调用。在 Post Agent 中，`{{ "{% credential aria2rpc"  }} %}` 为 aria2 JSONRPC 地址，`{{ "{% credential aria2token " }} %}` ` 为 aria2 JSONRPC 的令牌

## 大功告成

### 配置文件下载
附上两个 agent 的配置文件
- [https://bot.henry.wang/scenarios/6/export.json](https://bot.henry.wang/scenarios/6/export.json){:target="_blank"}


## TL;DR Post Agent 2 - 推送触发通知

Post Agent 同样可以用于推送触发通知，原理同上都是通过 http post，[Telegram](https://core.telegram.org/bots/api#making-requests){:target="_blank"} 和 [Slack](https://api.slack.com/incoming-webhooks#sending_messages){:target="_blank"} 都有对应的 API ，自行摸索即可。

之前写了一个 Slack 的误删了，就懒得再写了。

##### 注
[^1]: 其他非重要的对应选项请参照 [Huginn 官方 wiki](https://github.com/huginn/huginn/wiki){:target="_blank"}
