---
title: "Magical Ability to Peek Inside a Running Kubernetes Cluster"
date: 2024-06-16T10:00:00+05:30
slug: "magical-ability-peek-inside-running-kubernetes-cluster"
description: "A practical walkthrough of using Tetragon on Minikube to observe process-level events from Kubernetes workloads."
tags: ["kubernetes", "ebpf", "tetragon", "observability", "containers"]
---

Adapted from my LinkedIn article: [Magical ability to peek inside running Kubernetes Cluster](https://www.linkedin.com/pulse/magical-ability-peek-inside-running-kubernetes-cluster-devidas-jadhav-t4eyf).

## Motivation

I have been exploring eBPF using `libbpf` (Go) and BCC (Python) for debugging.

Recently I started diving into Kubernetes internals (`runc`, `containerd`, `etcd`). I wanted a practical way to understand runtime behavior at process level, and Tetragon was on my list for a while. This post captures my first weekend experience running it on a laptop.

## Setup on laptop (Minikube + Helm + Tetragon)

I used Minikube for a local cluster and installed Tetragon using Helm.

```bash
#!/usr/bin/env bash
set -euo pipefail

# Install minikube if missing
if ! command -v minikube >/dev/null 2>&1; then
  curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
  sudo install minikube-linux-amd64 /usr/local/bin/minikube
  rm -f minikube-linux-amd64
fi

# Start cluster
if ! minikube status | grep -q "Running"; then
  minikube start --driver=kvm2
fi

# Install helm if missing
if ! command -v helm >/dev/null 2>&1; then
  curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
  chmod 700 get_helm.sh
  ./get_helm.sh
  rm -f get_helm.sh
fi

# Install kubectl if missing
if ! command -v kubectl >/dev/null 2>&1; then
  curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
  sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
  rm -f kubectl
fi

# Install tetragon CLI if missing
if ! command -v tetragon >/dev/null 2>&1; then
  curl -LO https://github.com/cilium/tetragon/releases/latest/download/tetragon-linux-amd64
  sudo install tetragon-linux-amd64 /usr/local/bin/tetragon
  rm -f tetragon-linux-amd64
fi

# Add cilium repo and install tetragon chart
if ! helm repo list | grep -q "cilium"; then
  helm repo add cilium https://helm.cilium.io
fi
helm repo update

if ! helm list -n kube-system | grep -q "tetragon"; then
  helm install tetragon cilium/tetragon -n kube-system
fi

kubectl rollout status -n kube-system ds/tetragon -w --timeout=10m
```

## Test policy and event capture

Create a simple tracing policy:

```yaml
apiVersion: cilium.io/v1alpha1
kind: TracingPolicy
metadata:
  name: uprobe
spec:
  uprobes:
    - path: "/bin/bash"
      symbols:
        - readline
        - main
```

Apply policy and run a test pod:

```bash
kubectl apply -f test.yaml
kubectl run -it bash --image=bash:latest -- sh
```

In another terminal, stream Tetragon events:

```bash
kubectl logs -n kube-system -l app.kubernetes.io/name=tetragon -c export-stdout -f | jq
```

When you execute commands inside the `bash` pod, Tetragon emits process events including binary path, pod metadata, and execution context.

## What I liked

- Fast path from local setup to useful runtime visibility
- Rich event metadata connected directly to Kubernetes objects
- Great fit for security + observability learning loops

## Final thoughts

If you are learning Kubernetes internals and want to "see" process behavior in real time, Tetragon is a great hands-on starting point.
