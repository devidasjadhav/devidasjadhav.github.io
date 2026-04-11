---
title: "Container Internals Deep Dive 07: Rootless Containers with Podman"
date: 2022-01-30T09:00:00+05:30
slug: "container-internals-deep-dive-07-rootless-containers-podman"
description: "How rootless containers work with user namespaces, and where Podman fits in secure workflows."
cover: "https://images.unsplash.com/photo-1469474968028-56623f02e42e?auto=format&fit=crop&w=1600&q=80"
coverCredit: "Photo by Unsplash"
tags: ["rootless", "podman", "security", "containers"]
series: "Container Internals Deep Dive"
---

Series: **8/10**. In part 06 we compared `runc` and `crun`. Here we focus on rootless containers.

Rootless mode runs container workloads without host root privileges. This reduces host compromise impact.

## Core mechanics

- user namespaces map container UIDs to unprivileged host ranges
- root inside container != root on host
- networking often uses slirp4netns or rootless CNI variants
- storage uses user-scoped paths instead of global daemon paths

## Why Podman is common here

Podman supports daemonless workflows and rootless operation out of the box.
It is popular for developer desktops, CI workers, and hardened multi-user environments.

## Typical setup checks

```bash
podman info --debug
cat /etc/subuid
cat /etc/subgid
```

If subordinate ID mappings are missing, rootless container creation will fail or degrade.

## Tradeoffs to understand

1. Some kernel/network features have limitations in rootless mode.
2. Throughput can differ from rootful networking paths.
3. Debugging path differs from daemon-centric Docker setups.

## Takeaway

Rootless containers are one of the highest-leverage hardening steps when the workload model allows it.

Next: [Container Internals Deep Dive 08: Kata Containers](/posts/container-internals-deep-dive-08-kata-containers/)
