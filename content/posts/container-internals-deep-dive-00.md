---
title: "Container Internals Deep Dive 00"
date: 2021-12-12T12:00:03+05:30
slug: "container-internals-deep-dive-00"
description: "Kickoff post for a deep-dive series on container internals, starting with why chroot matters."
tags: ["containers", "docker", "chroot", "containerd", "runc"]
---

Past year I have been looking under the hood of Docker. I came across multiple interesting things.
This series is my attempt to share whatever I have learned during past year.
I expect this series will start new discussions rather than only providing answers.

## Before we begin

This series is intended for those who are interested in understanding "How Docker works?".
For readers who just want to run Docker, getting started can be as easy as:

```bash
docker pull <image-name>   # pull from Docker Hub
docker run <image-name>    # run local image; pulls if missing
```

```bash
$ sudo docker run -it ubuntu bash
[sudo] password for dev:
Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
7b1a6ab2e44d: Pull complete
Digest: sha256:626ffe58f6e7566e00254b638eb7e0f3b11d4da9675088f4781a50ae288f3322
Status: Downloaded newer image for ubuntu:latest
root@3fdeef5a4a1e:/#
```

That is enough to get moving. If you want to know what happens behind the scenes, welcome to this series.

## History of Docker

Why are we learning history of containers?

Because history is the best way to understand how we got here. It helps answer:

- Why do we do what we do?
- Why are containers designed this way?

## What are containers?

There are many definitions. This one is from the [Docker website](https://www.docker.com/resources/what-container):

> A container is a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another. A Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings.

Important part: *container image is a lightweight, standalone, executable package of software*.

Container is simply a process running with:

1. Its own just-enough root filesystem
2. Bare minimum privileges
3. A sandboxed environment so it will not harm the host

## Chroot

It all started with `chroot` system call.
`chroot` was added to Unix by Bill Joy in 1982, with intention to test installation and build systems.

From the man page (`man 2 chroot`):

> chroot - run command or interactive shell with special root directory

In short, `chroot` allows you to switch your root filesystem (that is, what you see at `ls /`).

## How chroot relates to containers

Note: Links below point to source code you might enjoy exploring.

[Moby Project](https://github.com/moby/moby) is an open-source project created by Docker to enable and accelerate software containerization.

Moby/Docker uses containerd to manage containers through [libcontainerd](https://github.com/moby/moby/tree/master/libcontainerd).

Libcontainerd init uses [go-runc](https://github.com/containerd/containerd/blob/master/pkg/process/init.go#L38).

go-runc is a wrapper for [runc](https://github.com/opencontainers/runc).

runc [init](https://github.com/opencontainers/runc/blob/master/init.go#L13) eventually leads to [chroot](https://github.com/opencontainers/runc/blob/master/libcontainer/rootfs_linux.go#L141).

Summary:

1. Moby/Docker CLI is a frontend talking to `dockerd` on Unix socket (`/var/run/docker.sock`).
2. `containerd` communicates with `dockerd` over gRPC API exposed by [containerd](https://github.com/containerd/containerd/blob/main/services/server/server.go#L104).
3. `containerd` handles tasks like pulling/storing images and starting containers.
4. For runtime execution, it uses `runc` (via go-runc wrapper).
5. `runc` uses `chroot` while spawning new containers.

## So what?

I could have just said Docker uses `chroot`. But I was interested in the full flow of how a new container is spawned.

Containers use `chroot` for a specific purpose.

## Why change root in first place?

Changing root is necessary to solve one of Docker's primary goals:
the classic "It works for me!" problem.

To make workloads reproducible, we run them with a dedicated root filesystem containing all required dependencies.
With `chroot`, the process uses container root filesystem resources instead of host root filesystem resources.

## And much more

This post was about `chroot` and kickoff for the series.
The original plan for next posts:

1. Chroot (already discussed above)
2. Cgroups (limit resource usage)
3. Namespace (isolating containers running on same system)
4. Network Namespaces and CNI (critical part of container ecosystem)
5. containerd (details and internals)
6. OCI standard (why and what of OCI)
7. runc and crun (implementations of OCI and comparing them)
8. Rootless containers (case study using podman)
9. Kata containers
10. Firecracker microVM

These are the first 10 planned topics. See you in the next post about chroot.
