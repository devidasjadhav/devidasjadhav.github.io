---
title: "Container Internals Deep Dive 02: Namespaces"
date: 2021-12-26T09:00:00+05:30
slug: "container-internals-deep-dive-02-namespaces"
description: "How Linux namespaces isolate process IDs, mounts, users, and networking for containers."
tags: ["containers", "namespaces", "linux", "isolation"]
---

Series: **3/10**. In part 01 we covered cgroups. In this part we cover namespaces.

Namespaces isolate what a process can see. They are a major reason containers feel like lightweight VMs.

## Core namespace types

- `pid`: process ID tree isolation
- `mnt`: mount table isolation
- `net`: network stack isolation
- `uts`: hostname/domain isolation
- `ipc`: shared memory and message queue isolation
- `user`: user/group ID mapping

## Practical mental model

Cgroups answer: "How much can this process use?"
Namespaces answer: "What can this process see?"

Containers need both.

## Inspect namespace boundaries

```bash
docker run --rm -d --name ns-demo alpine sleep 600
PID=$(docker inspect ns-demo --format '{{.State.Pid}}')
sudo ls -l /proc/$PID/ns
```

You will see distinct namespace handles such as `mnt:[402653xxxx]` and `net:[402653xxxx]`.

Compare host and container network namespaces:

```bash
readlink /proc/1/ns/net
sudo readlink /proc/$PID/ns/net
```

## Why user namespaces are special

User namespaces remap container UID 0 (root) to unprivileged IDs on host.
This is foundational for rootless containers and stronger multitenant isolation.

## Security caveat

Namespaces reduce blast radius but are not complete security by themselves.
Pair them with seccomp, AppArmor/SELinux, dropped capabilities, and image hardening.

## Takeaway

Namespaces provide visibility isolation. Cgroups provide resource isolation. Together they define container boundaries.

Next: [Container Internals Deep Dive 03: Network Namespaces and CNI](/posts/container-internals-deep-dive-03-network-namespaces-cni/)
