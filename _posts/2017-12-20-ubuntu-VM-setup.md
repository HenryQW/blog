---
key: 20171220
modify_date: 2017-12-20
tags: [Ubuntu, VM, English]
title: Ubuntu VM setup tips for VirtualBox
---

Steps to remember, in case .vdi corrupts (again). Such a painful process!

<!--more-->

Before installing GuestAdditions you should run these, otherwise shared folder and clipboard may not work.

{% highlight sh %}
sudo apt update && upgrade -y

reboot

sudo usermod -a -G vboxsf henry
{% endhighlight %}

These are for installing packages behind corporate proxy.
{% highlight sh %}
export http_proxy=http://192.168.10.15:8080
sudo echo 'Acquire::http::Proxy "http://192.168.10.15:8080"' >> /etc/apt/apt.conf
sudo apt install zlib1g-dev ruby-dev git gcc ake 
sudo gem install bundler --http-proxy http://192.168.10.15:8080
bundle install
{% endhighlight %}
