---
title: "Why a Linux Kernel Engineer Is Learning Cloud"
date: 2021-06-13T10:00:00+05:30
slug: "why-linux-kernel-engineer-learning-cloud"
description: "From eBPF debugging to cloud-native systems: why kernel experience naturally extends into modern cloud infrastructure."
cover: "https://images.unsplash.com/photo-1469474968028-56623f02e42e?auto=format&fit=crop&w=1600&q=80"
coverCredit: "Photo by Unsplash"
tags: ["linux", "cloud", "ebpf", "kubernetes", "terraform"]
---

Adapted from my LinkedIn article: [Why Linux Kernel engineer is learning Cloud ?](https://www.linkedin.com/pulse/why-linux-kernel-engineer-learning-cloud-devidas-jadhav).

I had spent around eight years solving Linux kernel and low-level systems problems. Then one debugging incident changed my direction.

## The trigger: memory leak debugging

We were debugging a memory leak in a large daemon-based project.

- We considered `valgrind`, but startup constraints made it hard for this workflow.
- We explored `libleak` with `LD_PRELOAD`, but the raw logs were noisy and operationally heavy.
- Then we evaluated BCC and eBPF-based approaches, which gave us a cleaner path.

That moment sparked a deeper interest in eBPF-based observability.

## Why this led to cloud

As I explored eBPF projects, I noticed many practical use cases were cloud-native:

- service-level visibility in microservice systems
- runtime security and policy enforcement
- container and Kubernetes-level telemetry

I was always interested in understanding internals (`cgroups`, namespaces, runtimes), and cloud-native systems gave those internals real production context.

## Learning approach that worked for me

I started with practical, project-first learning instead of only theory.

One weekend, I explored Terraform on AWS using a follow-along style course and made a few deliberate choices:

- built a custom VPC instead of relying only on defaults
- preferred dynamic AMI lookup instead of hardcoding
- experimented with Ubuntu + Apache instead of the exact demo stack

This made the concepts stick without getting overwhelmed.

## Why kernel-to-cloud is a useful path

Kernel engineers already carry strong foundations in Linux, networking, and process behavior.
Cloud-native systems build on those same primitives, just at orchestration scale.

The shift is less about abandoning low-level depth, and more about applying it where distributed systems create new failure modes.

## Final note

This transition helped me connect low-level debugging instincts with modern platform engineering.
If you are from a kernel background and curious about cloud, your existing skills transfer more directly than you might expect.
