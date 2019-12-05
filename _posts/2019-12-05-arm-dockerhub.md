---
key: 20191205
modify_date: 2019-12-05
title: Build arm images on dockerhub
cover: /assets/images/2019-12-05/armdocker.jpg
tags: [Docker, English]
---

Dockerhub by default doesn't support building arm-based images directly, but here is a trick to do so.

<!--more-->

---

The solution is taken from Davide on GitHub[^1], and it's building arm-based tags for [Awesome-TTRSS on dockerhub](https://hub.docker.com/r/wangqiru/ttrss/builds) perfectly.

We first create a folder `hooks` at the root directory, and,

1. A `post_checkout` file with the following content:

    ```bash
    #!/bin/bash
    curl -L https://github.com/balena-io/qemu/releases/download/v3.0.0%2Bresin/qemu-3.0.0+resin-arm.tar.gz | tar zxvf - -C . && mv qemu-3.0.0+resin-arm/qemu-arm-static .
    ```

    This instructs dockerhub to pull the qemu emulator[^2] by balena.io and untar it.

1. A `pre_build` file with the following content:

    ```bash
    #!/bin/bash
    docker run --rm --privileged multiarch/qemu-user-static:register --reset
    ```

    This instructs dockerhub to execute the register script for the emulator with the binfmt module.

1. Add the following to the very top of your Dockerfile, right after the `From` statement:

    ```bash
    COPY qemu-arm-static /usr/bin
    ```

    This instructs dockerhub to copy the resulting qemu-arm-static binary to the building instance.

dockerhub should happily builds your arm-based images now.

[^1]: https://github.com/docker/hub-feedback/issues/1261#issuecomment-441126749
[^2]: https://github.com/balena-io/qemu