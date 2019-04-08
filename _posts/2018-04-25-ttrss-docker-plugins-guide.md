---
key: 20180425
modify_date: 2019-02-18
tags: [Docker, TTRSS, English]
title: A ttrss setup guide - Start your own RSS aggregator today
cover: /assets/images/2018-04-25/ttrss.png
---

This is a detailed guide to setup a fully running ttrss instance and explore its potential.

The docker image is available at [GitHub](https://github.com/HenryQW/docker-ttrss-plugins){:target="_blank"} and [Docker Hub](https://hub.docker.com/r/wangqiru/ttrss/){:target="_blank"}.

![Docker Pulls](https://img.shields.io/docker/pulls/wangqiru/ttrss.svg)
![Docker Stars](https://img.shields.io/docker/stars/wangqiru/ttrss.svg)

![Docker Automated build](https://img.shields.io/docker/automated/wangqiru/ttrss.svg)
![Docker Build Status](https://img.shields.io/docker/build/wangqiru/ttrss.svg)

<!--more-->

---

## Introduction

Approximately 2 years ago I started using Docker to deploy my services, back then I was new to Docker and thus I used [rubenv's](https://github.com/rubenv/docker-ttrss-plugins){:target="_blank"} ttrss docker image. Later on I wrote a little PHP plugin for ttrss, [Mercury](https://github.com/HenryQW/mercury_fulltext){:target="_blank"}, which utilises [the API](https://mercury.postlight.com){:target="_blank"} from Postlight to extract fulltext of RSS feeds, as a replacement for that non-performant af_readability provided by ttrss. And I decided to integrate it into the image.

Today the image is hitting over 100k pulls which surprises me, probably due to the recent Facebook & Cambridge Analytica scandal and the article [It's Time For an RSS Revival](https://www.wired.com/story/rss-readers-feedly-inoreader-old-reader/){:target="_blank"} by Wired.

### Requirements

Bear in mind that this docker image isn't fully self-contained, it requires a database to work. If you want to assign it with an URL, you need an nginx web server too.

If you are using a machine with limited resources (RAM < 512MB) then you'd better not host other services, otherwise it might not be working as intended.

Personally I'd recommend running with Nginx and PostgreSQL.

## Deployment and Configuration

### The Easy Way

#### docker-compose

There is an easy way to deploy which is via `docker-compose`. It was made available on 15 May [here](https://github.com/HenryQW/docker-ttrss-plugins/blob/master/docker-compose.yml){:target="_blank"}.

It's as simple as download [the file](https://github.com/HenryQW/docker-ttrss-plugins/blob/master/docker-compose.yml){:target="_blank"} and run:
{% highlight bash %}
docker-compose up -d
{% endhighlight %}

However, an nginx instance will most likely be required and a sample configuration is provided [below](#nginx).

### The Not-So-Easy Way

The not-so-easy way is to deploy two docker containers separately.

#### PostgreSQL

> If you already have a PostgreSQL instance running (docker or non-docker), you may skip this part.

I chose this image because it allows you to specify the extensions you want to enable. `pg_trgm` is required for marking similar feeds as read via the ttrss plugin `af_psql_trgm`.

The default user is `postgres`.

It persists its data in directory `/docker/postgres/data/`.

{% highlight bash %}
docker run -d --name postgres --restart=always \
-v /docker/postgres/data/:/var/lib/postgresql/ \
-e PG_PASSWORD=mydbpass \
-e DB_EXTENSION=pg_trgm \
-p 5432:5432 \
sameersbn/postgresql:latest
{% endhighlight %}

#### TTRSS

This links ttrss with the PostgreSQL container created just now and exposes port 181 to the public.

The default credential is `admin` and `password`. You should be prompted to change them upon first login, please do so.

{% highlight bash %}
docker run -dit --name=ttrss --restart=always \
-e SELF_URL_PATH=https://ttrssdev.henry.wang \
-e DB_HOST=postgres  \
-e DB_PORT=5432  \
-e DB_NAME=myttrss  \
-e DB_USER=postgres  \
-e DB_PASS=mydbpass  \
-p 181:80  \
wangqiru/ttrss
{% endhighlight %}

If you are connecting ttrss to an existing postgresl container, it will not work properly at this stage because there is one more thing you need to setup: the connection between two containers.

> There is a shortcut flag `--link` command. However, I'm not going to use this as it is a legacy command and may eventually be removed.[^1]

The recommended approach is to use docker network.

<ol>
<li>Create a docker network named ttrss_network</li>

{% highlight bash %}
docker network create ttrss_network
{% endhighlight %}

<li>Connect both container to ttrss_network</li>
{% highlight bash %}
docker network connect ttrss_network postgres
docker network connect ttrss_network ttrss
{% endhighlight %}

<li>Restart ttrss container for the network to take effect</li>
{% highlight bash %}
docker restart ttrss
{% endhighlight %}
</ol>

Alternatively, if you have an existing docker network, you can spin up TTRSS in this way:
{% highlight bash %}
docker run -dit --name=ttrss --restart=always \
--net your_network_name \
-e SELF_URL_PATH=https://ttrssdev.henry.wang \
-e DB_HOST=your_postgres_container_name  \
-e DB_PORT=5432  \
-e DB_NAME=myttrss  \
-e DB_USER=postgres  \
-e DB_PASS=mydbpass  \
-p 181:80  \
wangqiru/ttrss
{% endhighlight %}

#### Nginx

This is an nginx config that forces all traffics through https and reverse proxies all requests into the ttrss container (inside it has its own nginx server listening to requests) created above.

ssl certificate can be obtained for free [here at Let's Encrypt](https://letsencrypt.org){:target="_blank"}.

{% highlight nginx %}
upstream ttrssdev {
    server 127.0.0.1:3100;
}

server {
    listen 80;
    server_name  ttrssdev.henry.wang;
    return 301 https://ttrssdev.henry.wang$request_uri;
}

server {
    listen 443 ssl;
    gzip on;
    server_name  ttrssdev.henry.wang;

    ssl_certificate /etc/letsencrypt/live/ttrssdev.henry.wang/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/ttrssdev.henry.wang/privkey.pem;

    access_log /var/log/nginx/ttrssdev_access.log combined;
    error_log  /var/log/nginx/ttrssdev_error.log;

    location / {
        proxy_redirect off;
        proxy_pass http://ttrssdev;

        proxy_set_header  Host                $http_host;
        proxy_set_header  X-Real-IP           $remote_addr;
        proxy_set_header  X-Forwarded-Ssl     on;
        proxy_set_header  X-Forwarded-For     $proxy_add_x_forwarded_for;
        proxy_set_header  X-Forwarded-Proto   $scheme;
        proxy_set_header  X-Frame-Options     SAMEORIGIN;

        client_max_body_size        100m;
        client_body_buffer_size     128k;

        proxy_buffer_size           4k;
        proxy_buffers               4 32k;
        proxy_busy_buffers_size     64k;
        proxy_temp_file_write_size  64k;
    }
}
{% endhighlight %}

### Plugin Settings

The docker image comes with three plugins now:

1. [Mercury](https://github.com/HenryQW/mercury_fulltext){:target="_blank"}
1. [Fever](https://github.com/HenryQW/tinytinyrss-fever-plugin){:target="_blank"}
1. [Feediron](https://github.com/feediron/ttrss_plugin-feediron){:target="_blank"}
1. [OpenCC](https://github.com/HenryQW/ttrss_opencc){:target="_blank"}

#### Mercury

~~It utilizes [Mercury Parser](https://mercury.postlight.com/web-parser/){:target="_blank"} to extract the full content for feeds. An API key is required to use this plugin, which is available for free [here](https://mercury.postlight.com/web-parser/){:target="_blank"}.~~

Mercury Parser API has stopped providing new API keys and existing keys will stop working on April 15, 2019. It was made open source on GitHub: [postlight/mercury-parser-api](https://github.com/postlight/mercury-parser-api){:target="_blank"} and you can host your own Mercury Parser instance. I've made a dockerized self-host solution,[HenryQW/mercury-parser-api](https://github.com/HenryQW/mercury-parser-api){:target="_blank"}, which is now a part of [the docker-compose file](https://github.com/HenryQW/docker-ttrss-plugins/blob/master/docker-compose.yml){:target="_blank"}.

Steps to configure:

1. Enable the plugin *mercury_fulltext* in **Preferences/Plugins**.
1. Save your *Mercury API Endpoint* in the *Mercury_fulltext settings* under **Feeds** tab.
1. Configure for feeds under **Plugins** tab of the **Edit Feed** window (you can right click your feed to get there).

#### Fever

This simulates ttrss as a fever API, which is supported by many RSS readers. It is a part of [the docker-compose file](https://github.com/HenryQW/docker-ttrss-plugins/blob/master/docker-compose.yml){:target="_blank"}.

Steps to configure:

1. Check *Enable API access* in **Preferences/Preferences/General**.
1. Enable the plugin *fever* in **Preferences/Plugins**.
1. Enter a new password for accessing fever in **Preferences/Preferences/Fever Emulation**.
1. Access the emulated fever API via *https://yoururl.com/plugins/fever* with your ttrss user and the password set in step 3.

#### Feediron

If Mercury doesn't work well with the feed, you can use this to achieve the same goal.

Unfortunately I'm not using this plugin because I use [Huginn](https://github.com/huginn/huginn){:target="_blank"} which provides more functionalities I need for other purposes (other than full text extraction). Please visit [its github](https://github.com/feediron/ttrss_plugin-feediron){:target="_blank"} for more information.

#### OpenCC

A plugin for conversion from Traditional to Simplified Chinese based on [OpenCC by BYVoid](https://github.com/BYVoid/OpenCC){:target="_blank"}

Steps to configure:

1. Enable the plugin *OpenCC* in **Preferences/Plugins**.
1. Save your *OpenCC API Address* in the *OpenCC settings* under **Feeds** tab.
1. Configure for feeds under **Plugins** tab of the **Edit Feed** window (you can right click your feed to get there).

There two demo instances for this plugin (availability is **not** guaranteed): [https://opencc.henry.wang](https://opencc.henry.wang) or [http://opencc2.henry.wang](http://opencc2.henry.wang)

Preferably, deploy your own OpenCC API Server via Docker:

{% highlight sh %}
docker run -d -p 3000:3000 wangqiru/opencc_api_server
{% endhighlight %}

## Conclusion

Now you should have a working ttrss instance. Forget algorithm-recommended newsfeeds/timelines, begin adding your feed!

[^1]: [Legacy container links](https://docs.docker.com/network/links/){:target="_blank"}
