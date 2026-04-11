---
title: "Container Internals Deep Dive 04: containerd Internals"
date: 2022-01-09T09:00:00+05:30
slug: "container-internals-deep-dive-04-containerd-internals"
description: "Understand containerd architecture: content store, snapshotters, runtime shims, and lifecycle flow."
tags: ["containerd", "containers", "runtime", "kubernetes"]
---

Series: **5/10**. In part 03 we covered networking and CNI. Now we go deeper into `containerd`.

`containerd` is a core container runtime component used directly by Kubernetes (via CRI plugins) and indirectly by Docker.

## High-level architecture

- **content store**: blob storage for image layers
- **metadata store**: object references and lifecycle metadata
- **snapshotters**: provide mountable filesystem views (overlayfs, stargz, etc.)
- **runtime shims**: process supervisors between containerd and OCI runtime
- **task service**: create/start/kill process lifecycle

## Lifecycle flow (simplified)

1. Pull image -> store blobs in content store.
2. Unpack image -> snapshotter prepares rootfs.
3. Create task -> shim starts OCI runtime.
4. Start task -> process runs with configured isolation.
5. Collect events, logs, exits via shim and containerd APIs.

## Useful `ctr` commands

```bash
sudo ctr images ls
sudo ctr containers ls
sudo ctr tasks ls
sudo ctr snapshots ls
```

For Kubernetes nodes, `crictl` is usually better than raw `ctr` for day-2 ops.

## Why shims exist

Shims decouple container processes from the containerd daemon lifecycle.
If containerd restarts, running containers can continue, and reconnect happens cleanly.

## Tuning areas teams often miss

1. Snapshotter choice (performance vs compatibility).
2. Garbage collection policies.
3. Registry mirror configuration for pull resilience.
4. RuntimeClass strategy for mixed workload security tiers.

## Takeaway

Treat `containerd` as a platform component, not a black box. Understanding its stores and shims pays off during incidents.

Next: [Container Internals Deep Dive 05: OCI Standard](/posts/container-internals-deep-dive-05-oci-standard/)
