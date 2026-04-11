---
title: "Container Internals Deep Dive 01: Cgroups"
date: 2021-12-19T09:00:00+05:30
slug: "container-internals-deep-dive-01-cgroups"
description: "How cgroups enforce resource limits for CPU, memory, and I/O in container workloads."
tags: ["containers", "cgroups", "linux", "docker", "kubernetes"]
---

Series: **2/10**. In part 00 we covered `chroot`. In this part we cover cgroups.

Cgroups (control groups) are the Linux kernel mechanism used to account for and limit resources for a set of processes.

## Why cgroups matter for containers

Without cgroups, one noisy workload can consume all CPU or memory on a host.
With cgroups, each container can be bounded and monitored.

Typical controls:

- `cpu.max` (v2): CPU quota and period
- `memory.max`: hard memory limit
- `memory.high`: soft memory pressure threshold
- `pids.max`: process count cap
- `io.max`: block I/O limits

## cgroups v1 vs v2 (quick view)

- v1: separate hierarchies per controller; operationally messy
- v2: unified hierarchy; cleaner semantics and pressure reporting

Most modern distros and Kubernetes setups are moving to cgroups v2.

## Hands-on: inspect a running container

```bash
docker run --rm -d --name cgroup-demo --memory=256m --cpus=1 nginx:stable
docker inspect cgroup-demo --format '{{.HostConfig.Memory}} {{.HostConfig.NanoCpus}}'
docker exec cgroup-demo sh -c 'cat /proc/self/cgroup'
```

On cgroups v2 hosts, inspect limits from host namespace:

```bash
CID=$(docker inspect cgroup-demo --format '{{.Id}}')
CG=/sys/fs/cgroup/system.slice/docker-${CID}.scope
sudo cat "$CG/memory.max"
sudo cat "$CG/cpu.max"
```

## Common operational mistakes

1. Setting only memory limit but no CPU cap, causing host contention.
2. Setting tight memory limits without profiling peak RSS.
3. Ignoring `memory.high`, which helps graceful reclaim before OOM.

## Kubernetes mapping

Kubernetes `requests` and `limits` translate to cgroup controls via the runtime.

- CPU `limit` -> quota (`cpu.max`)
- Memory `limit` -> `memory.max`
- Pod-level and container-level constraints are composed by kubelet and runtime

## Takeaway

Cgroups are the resource contract for containers. If your SLOs matter, cgroup tuning is not optional.

Next: [Container Internals Deep Dive 02: Namespaces](/posts/container-internals-deep-dive-02-namespaces/)
