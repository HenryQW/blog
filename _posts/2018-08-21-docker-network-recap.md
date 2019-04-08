---
key: 20180821
modify_date: 2018-09-04
tags: [Docker, English]
title: Docker Network Recap
---

Recap on [Docker's Networking](https://docs.docker.com/network/), mostly Bridge Network.

<!--more-->

---

## Why

More than ever before, Docker is being adopted in both work (thank god) and personal project. It's painless to spin up a disposable container for testing business logic involves PostgreSQL, WebDAV or anything. And it's NINJA for deploying services for someone who doesn't know a thing about computers.

I found myself constantly explaining docker network related things to others, such as *how does it know which xxx instance to connect*.

## What I've Recapped

### Bridge Network

So far the most commonly used network mode by me, I found it works perfectly for small to medium stack. Bridge can be further divided into `default` and `user-defined` networks.

#### Default

> All containers were born bridged. -- Arisdocker

That is, if you don't specify anything, this default bridge driver will be used.

Default bridge driver doesn't provide functionalities like `service discovery` and `DNS resolution`. Which means they can't talk to each other directly via container name, unless:

1. they are linked via `--link` flag, this is being deprecated[^1] thus not recommended.
1. the connection is established via IP address.

#### User-Defined

Unlike default, user-defined bridge network allows containers on the same to interpolate.

<ol>
<li>Create a docker network named `my-net`</li>

{% highlight bash %}
docker network create my-net
{% endhighlight %}

<li>Create containers that connects to `my-net`</li>
{% highlight bash %}
docker run -dit --name=alpine1 \
--net=my-net \
alpine ash

docker run -dit --name=alpine2 \
--net=my-net --net-alias=a2 \
alpine ash
{% endhighlight %}

<li>Create another container that doesn't connect to `my-net`</li>
{% highlight bash %}
docker run -dit --name=alpine3 \
alpine ash
{% endhighlight %}
</ol>

The above containers:

1. alpine1 can reach alpine2 by `ping alphine2` or `ping a2`.
1. alpine2 can reach alpine1 by `ping alphine1`.
1. alpine3 cannot reach anyone.

On top of that, containers within the same user-defined bridge network open up all ports to each other.

### Host Network

Host network is as straightforward as the name suggests, all containers with a host network will have no network isolation, thus can be directly accessed via host_ip:container_port. Although no `service discovery` and `DNS resolution`will be provided, containers can use public ip address to communicate with each other.

{% highlight bash %}
docker run -dit --name=postgres \
--net=host \
postgres
{% endhighlight %}

`--net=host` allows the above postgres instance to be accessible via host_ip:5432.

Everything else (files, processes) will be isolated just like normal.

[^1]: [Legacy container links](https://docs.docker.com/network/links/){:target="_blank"}