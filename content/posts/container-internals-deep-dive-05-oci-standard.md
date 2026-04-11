---
title: "Container Internals Deep Dive 05: OCI Standard"
date: 2022-01-16T09:00:00+05:30
slug: "container-internals-deep-dive-05-oci-standard"
description: "Why OCI image and runtime specs matter for interoperability across tools and platforms."
cover: "https://images.unsplash.com/photo-1498050108023-c5249f4df085?auto=format&fit=crop&w=1600&q=80"
coverCredit: "Photo by Unsplash"
tags: ["oci", "containers", "standards", "runtime"]
series: "Container Internals Deep Dive"
---

Series: **6/10**. In part 04 we covered containerd internals. Here we cover OCI.

OCI (Open Container Initiative) gives us common specs so build tools, registries, and runtimes can interoperate.

## Two specs you should know

1. **OCI Image Spec**: how images and layers are described.
2. **OCI Runtime Spec**: how a filesystem bundle + config becomes a running container.

Without this standardization, each vendor stack would be incompatible.

## Image spec in practice

- Manifest points to config + layers.
- Config includes entrypoint, env, labels, and rootfs metadata.
- Layers are content-addressed and immutable.

This enables reproducibility and deduplication across hosts and registries.

## Runtime spec in practice

Runtime spec defines `config.json` fields for:

- process args/env/cwd
- mount points
- Linux namespaces/cgroups/capabilities
- seccomp and additional restrictions

OCI runtimes (`runc`, `crun`, others) implement this contract.

## Why this matters to teams

1. You can swap runtime implementations with less friction.
2. Security tooling can reason about common metadata and behavior.
3. CI/CD and registry workflows remain portable across platforms.

## Takeaway

OCI is the compatibility layer that makes the container ecosystem practical at scale.

Next: [Container Internals Deep Dive 06: runc vs crun](/posts/container-internals-deep-dive-06-runc-vs-crun/)
