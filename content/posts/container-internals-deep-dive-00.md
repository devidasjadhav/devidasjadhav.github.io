---
title: "Container Internals Deep Dive 00"
date: 2021-12-12T12:00:03+05:30
slug: "container-internals-deep-dive-00"
description: "Kickoff post for a deep-dive series on container internals, starting with why chroot matters."
tags: ["containers", "docker", "chroot", "containerd", "runc"]
---

Series: **1/10**. This is the kickoff for the container internals deep-dive sequence.

Over the past year, I have been looking under the hood of Docker and related runtimes.
This series is my attempt to share what I learned along the way.
My goal is to start useful discussions, not just provide one-line answers.

## Before we begin

This series is for readers who want to understand *how Docker works*.
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

There are many definitions. Here is one from the [Docker website](https://www.docker.com/resources/what-container):

> A container is a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another. A Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings.

Important part: *a container image is a lightweight, standalone, executable package of software*.

A container is best thought of as a process (or process group) running with:

1. Its own root filesystem view
2. Isolation primitives such as namespaces and cgroups
3. A constrained privilege model

## Chroot

One important early building block is the `chroot` system call.
`chroot` was added to Unix in 1979, originally to help with build and test workflows.

From the man page (`man 2 chroot`):

> chroot - run command or interactive shell with special root directory

In short, `chroot` changes a process view of `/` (what it sees as the root filesystem).
By itself, `chroot` is not a complete security boundary, but it is a useful filesystem isolation primitive.

## How chroot relates to containers

Note: Links below point to source code you might enjoy exploring.

[Moby Project](https://github.com/moby/moby) is an open-source project created by Docker to enable and accelerate software containerization.

Docker Engine uses `containerd` for core lifecycle tasks through [libcontainerd](https://github.com/moby/moby/tree/master/libcontainerd).

`containerd` invokes [go-runc](https://github.com/containerd/containerd/blob/master/pkg/process/init.go#L38) as part of runtime execution.

go-runc is a wrapper around [runc](https://github.com/opencontainers/runc).

`runc` [init](https://github.com/opencontainers/runc/blob/master/init.go#L13) eventually reaches rootfs setup paths including [chroot](https://github.com/opencontainers/runc/blob/master/libcontainer/rootfs_linux.go#L141).

Summary:

1. Docker CLI talks to `dockerd` over Unix socket (`/var/run/docker.sock`).
2. `dockerd` talks to `containerd` over containerd's gRPC API.
3. `containerd` handles tasks like image pull/storage and container lifecycle.
4. For OCI runtime execution, `containerd` uses `runc` (via go-runc wrapper).
5. `runc` sets up root filesystem and isolation primitives, where `chroot` can be part of that setup.

## So what?

I could have stopped at "Docker uses `chroot`", but the full spawn path is more interesting and more useful for debugging.

The key point: containers are not *just* `chroot`; they combine filesystem setup, namespaces, cgroups, and capability controls.

## Why change root in first place?

Changing the root view helps solve one of Docker's core goals:
avoid the classic "It works on my machine" problem.

To make workloads reproducible, we run them with an image-based root filesystem containing required dependencies.
With `chroot` and mount namespace setup, a process resolves paths inside container rootfs instead of host rootfs.

## And much more

This post is a kickoff for the series, with focus on why `chroot` still matters.
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

These are the first 10 planned topics. See you in the next post, where we start with cgroups.

Next: [Container Internals Deep Dive 01: Cgroups](/posts/container-internals-deep-dive-01-cgroups/)
