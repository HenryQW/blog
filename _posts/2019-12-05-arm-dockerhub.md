---
key: 20191205
modify_date: 2019-12-05
title: Build arm images on dockerhub
cover: /assets/images/2019-12-05/armdocker.jpg
tags: [Docker, English]
---

Dockerhub by default doesn't support building arm-based images directly, but here is a trick to do so.

![Cover](/assets/images/2019-12-05/armdocker.jpg)

<!--more-->

---

## Update: GitHub Actions for the Win

I've switched to GitHub Actions, which provide a far more superior experience than dockerhub. Below are the GitHub Actions used for releasing my Awesome-TTRSS.

```yml
{% raw %}
name: '[builder] CI for releases'

on:
    push:
        branches:
            - master

jobs:
    release:
        runs-on: ubuntu-latest
        steps:
        -
            name: Checkout
            uses: actions/checkout@v1
        -
            name: Dockerhub login
            env:
                DOCKER_PASSWORD: ${{ secrets.DOCKER_PASSWORD }}
                DOCKER_USERNAME: ${{ secrets.DOCKER_USERNAME }}
            run: |
                echo "${DOCKER_PASSWORD}" | docker login --username ${DOCKER_USERNAME} --password-stdin
        -
            name: Set up Docker Buildx
            id: buildx
            uses: crazy-max/ghaction-docker-buildx@v1
            with:
                version: latest
        -
            name: Build dockerfile (with push)
            run: |
                docker buildx build \
                --platform=linux/amd64,linux/arm/v7,linux/arm64 \
                --output "type=image,push=true" \
                --file ./Dockerfile . \
                --tag $(echo "${DOCKER_USERNAME}" | tr '[:upper:]' '[:lower:]')/ttrss:latest
{% endraw %}
```

The workflow utilizes [Docker's multi-arch support](https://www.docker.com/blog/multi-arch-all-the-things/) and it will:

1. Checkout the latest code.
1. Login into dockerhub.
1. Build a single docker image with a docker manifest file, which states all the architectures supported, with buildx.
1. Push the image to dockerhub.

## The dockerhub Way

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
