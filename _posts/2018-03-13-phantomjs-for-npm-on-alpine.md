---
key: 20180313
modify_date: 2018-03-13
tags: [Node, Docker, English]
title: Phantomjs for npm in alpine-based docker image
article_header:
  background_image: false
---

Make npm `phantomjs-prebuilt` package compatible with alpine-based docker image.

<!--more-->

---

## Problem

### musl-libc vs glibc

alpine uses `musl-libc` as its C library instead of `glibc` to shrink down the image size and improve performance [^1].

However phantomjs doesn't get compiled with `musl-libc` which cripples `phantomjs-prebuilt` node package inside the container. The cause was [pinned down](https://github.com/sgerrand/alpine-pkg-glibc/issues/13#issuecomment-194111154){:target="_blank"} to the missing `ld-linux-x86-64.so.2`.

![phantomjs error](/assets/images/2018-03-13/phantomjs-error.png)

It appears there is no fix for this even after so long.

- [alpine-pkg-glibc/issues/13](https://github.com/sgerrand/alpine-pkg-glibc/issues/13){:target="_blank"}
- [grafana-docker/issues/44](https://github.com/grafana/grafana-docker/issues/44){:target="_blank"}
- [phantomjs-node/issues/702](https://github.com/amir20/phantomjs-node/issues/702){:target="_blank"}

## Solution

### Dockerize

One approach is to [Dockerize](https://github.com/larsks/dockerize){:target="_blank"} phantomjs into linux executables inside a `glibc` compatible base (Debian or Ubuntu):

{% highlight bash %}
// download phantomjs
mkdir /tmp && cd /tmp
curl -o phantomjs.tar.bz2 \
    -L https://bitbucket.org/ariya/phantomjs/downloads/phantomjs-2.1.1-linux-x86_64.tar.bz2

// untar and dockerize phantomjs
tar -jxvf phantomjs.tar.bz2

dockerize -n -o phantomjsDir -e /tmp/phantomjs-2.1.1-linux-x86_64  \
    -a /bin/dash /bin/sh -a /usr/share/fonts /usr/share -a /etc/ssl /etc  \ 
    -a /etc/fonts /etc \
    /tmp/phantomjs-2.1.1-linux-x86_64 /usr/bin/curl

// tar the executables but without phantomjs binaries
cd phantomjsDir
tar -zcf phantomjs.tar.gz ./lib ./lib64 ./usr/lib
{% endhighlight %}

Which gives us a `phantomjs.tar.gz` with the following structure:

{% highlight raw%}
│── lib
│   └── x86_64-linux-gnu
│       ├── libcom_err.so.2
│       ├── libcrypto.so.1.0.0  
│       ├── libcrypt.so.1
│       ├── libc.so.6
│       ├── libdl.so.2
│       ├── libexpat.so.1
│       ├── libgcc_s.so.1
│       ├── libgcrypt.so.11
│       ├── libgpg-error.so.0
│       ├── libkeyutils.so.1
│       ├── libm.so.6
│       ├── libnss_compat.so.2
│       ├── libnss_dns.so.2
│       ├── libnss_files.so.2
│       ├── libpng12.so.0
│       ├── libpthread.so.0
│       ├── libresolv.so.2
│       ├── librt.so.1
│       ├── libssl.so.1.0.0
│       └── libz.so.1
├── lib64
│   └── ld-linux-x86-64.so.2
└── usr
│   └── lib
│       └── x86_64-linux-gnu
│           ├── libasn1.so.8
│           ├── libcurl.so.4
│           ├── libffi.so.6
│           ├── libfontconfig.so.1
│           ├── libfreetype.so.6
│           ├── libgnutls.so.26
│           ├── libgssapi_krb5.so.2
│           ├── libgssapi.so.3
│           ├── libhcrypto.so.4
│           ├── libheimbase.so.1
│           ├── libheimntlm.so.0
│           ├── libhx509.so.5
│           ├── libidn.so.11
│           ├── libk5crypto.so.3
│           ├── libkrb5.so.26
│           ├── libkrb5.so.3
│           ├── libkrb5support.so.0
│           ├── liblber-2.4.so.2
│           ├── libldap_r-2.4.so.2
│           ├── libp11-kit.so.0
│           ├── libroken.so.18
│           ├── librtmp.so.0
│           ├── libsasl2.so.2
│           ├── libsqlite3.so.0
│           ├── libstdc++.so.6
│           ├── libtasn1.so.6
│           └── libwind.so.0
{% endhighlight %}

Then mount `phantomjs.tar.gz` into the alpine image we are going to build. 

{% highlight bash %}
tar -zxf phantomjs.tar.gz /
{% endhighlight %}

`phantomjs-prebuilt` should now work fine in alpine-based docker containers. 

[^1]: [http://www.etalabs.net/compare_libcs.html](http://www.etalabs.net/compare_libcs.html){:target="_blank"}