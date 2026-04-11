---
title: "Container Internals Deep Dive 03: Network Namespaces and CNI"
date: 2022-01-02T09:00:00+05:30
slug: "container-internals-deep-dive-03-network-namespaces-cni"
description: "How container networking works from veth pairs to CNI plugin chains in Kubernetes."
tags: ["containers", "networking", "cni", "kubernetes"]
---

Series: **4/10**. In part 02 we covered namespaces. In this part we focus on network isolation and CNI.

Container networking starts with Linux primitives, then orchestration layers make it usable at scale.

## The basic packet path

1. Container runs in its own network namespace.
2. A `veth` pair links container namespace to host namespace.
3. Host-side interface is attached to bridge, overlay, or routing fabric.
4. NAT, policy, and routing decide packet forwarding.

## Where CNI fits

CNI (Container Network Interface) is a plugin contract used by Kubernetes runtimes.

When a Pod is created, kubelet/runtime invokes CNI plugins to:

- allocate IP
- create links/interfaces
- attach routes
- apply network policy hooks (plugin-dependent)

## Useful debugging commands

```bash
ip netns list
ip link show type veth
ip route
sudo nsenter -t <container-pid> -n ip addr
```

On Kubernetes nodes, also inspect CNI state:

```bash
ls /etc/cni/net.d/
ls /opt/cni/bin/
```

## Common failure modes

1. IPAM exhaustion (no available Pod CIDR).
2. MTU mismatch causing packet fragmentation issues.
3. Firewall/NAT rules drift after node upgrades.
4. CNI plugin mismatch across node pools.

## Design choices to make early

- Overlay vs routed network model
- eBPF-based dataplane vs iptables-heavy path
- Network policy model and default deny posture

## Takeaway

Container networking is Linux networking plus automation. If you can trace a packet, you can debug most incidents.

Next: [Container Internals Deep Dive 04: containerd Internals](/posts/container-internals-deep-dive-04-containerd-internals/)
