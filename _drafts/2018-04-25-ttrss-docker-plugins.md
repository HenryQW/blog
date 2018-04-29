---
layout: post
key: 20180425
modify_date: 2018-04-25
tags: [Docker, TTRSS, English]
title: A ttrss setup guide - Start your own RSS aggregator today
---

This is a detailed guide to setup a fully running ttrss instance and explore its potential.

The docker image is available at [GitHub](https://github.com/HenryQW/docker-ttrss-plugins){:target="_blank"} and [Docker Hub](https://hub.docker.com/r/wangqiru/ttrss/){:target="_blank"} 

![Docker Pulls](https://img.shields.io/docker/pulls/wangqiru/ttrss.svg)
![Docker Stars](https://img.shields.io/docker/stars/wangqiru/ttrss.svg)

![Docker Automated build](https://img.shields.io/docker/automated/wangqiru/ttrss.svg)
![Docker Build Status](https://img.shields.io/docker/build/wangqiru/ttrss.svg)

<!--more-->
# Introduction

Approximately 2 years ago I started using Docker to deploy my services, back then I was new to Docker and thus I used [rubenv's](https://github.com/rubenv/docker-ttrss-plugins){:target="_blank"} ttrss docker iamge. Later on I wrote a little PHP plugin for ttrss, [Mercury](https://github.com/HenryQW/mercury_fulltext){:target="_blank"}, which utilises [the API](https://mercury.postlight.com){:target="_blank"} from Postlight to extract fulltext of RSS feeds, as a replacement for that non-performant af_readability provided by ttrss. And I decided to integrate it into the image.

Today the image is hitting over 100k pulls which surprises me, probably due to the recent Facebook & Cambridge Analytica scandal and the article [It's Time For an RSS Revival](https://www.wired.com/story/rss-readers-feedly-inoreader-old-reader/){:target="_blank"} by Wired.



## Requirements

Bear in mind that this docker image isn't fully self-contained, it requires a database to work. If you want to assign it with an URL, you need a Nginx web server too.

If you are using a machine with limited resources (RAM < 512MB) then you'd better not host other services, otherwise it might not be working as intended.

Personally I'd recommend running with Nginx and Postgresql.

## Configuration

### Postgresql

### Ngxin

### ttrss
{% highlight bash %}
docker run -it --name ttrss --restart=always \
--link [ your DB container ]:db  \
-e SELF_URL_PATH = [ your URL ]  \
-e DB_USER = [ your DB user ]  \
-e DB_PASS = [ your DB password ]  \
-p [ your port ]:80  \
-d wangqiru/ttrss
{% endhighlight %}

This is all you need to get ttrss up and running. However, in most cases 

### Plugins

The docker image comes with three plugins now:
1. [Mercury](https://github.com/HenryQW/mercury_fulltext){:target="_blank"}
2. [Fever](https://github.com/HenryQW/tinytinyrss-fever-plugin){:target="_blank"}
3. [Feediron](https://github.com/feediron/ttrss_plugin-feediron){:target="_blank"}


### Mercury

### Fever

### Feediron

## Further Extension