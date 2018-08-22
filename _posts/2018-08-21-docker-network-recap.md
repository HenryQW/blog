---
key: 20180821
modify_date: 2018-08-22
tags: [Docker, English]
title: Docker Network Recap
---

Recap on [Docker's Networking](https://docs.docker.com/network/), mostly Bridge Network.

<!--more-->
# Why

More than ever before, Docker is being adopted in both work (thank god) and personal project. It's painless to spin up a disposable container for testing business logic involves PostgreSQL, WebDAV or anything. And it's NINJA for deploying services for someone who doesn't know a thing about computers.

I found myself constantly explaining docker network related things to others, such as *how does it know which xxx instance to connect*.

# What I've Recapped
## Bridge Network
So far the most commonly used network mode by me, I found it works perfectly for small to medium stack. Bridge can be further divided into `default` and `user-defined` networks.

### Default Bridge Network

> All containers were born bridged. -- Arisdocker



### User-Defined Bridge Network


# Conclusion

