---
title: "Why I Chose GCP over AWS and Azure for Ollama and Open WebUI"
date: 2024-05-26T10:00:00+05:30
slug: "why-i-chose-gcp-for-ollama-open-webui"
description: "A hands-on comparison of cloud GPU spot workflows for running Ollama and Open WebUI."
tags: ["gcp", "aws", "azure", "ollama", "open-webui", "cloud"]
---

Adapted from my LinkedIn article: [Why I chose GCP over AWS and Azure for ollama and open webui ?](https://www.linkedin.com/pulse/why-i-chose-gcp-over-aws-azure-ollama-open-webui-devidas-jadhav-y8aef).

I wanted to run Ollama + Open WebUI in cloud with spot pricing. Local hardware was not giving the performance I wanted.

## Evaluation goal

- run GPU-backed spot instance
- keep cost predictable
- deploy quickly without platform friction

## AWS experience

I started with AWS because I already used it for Kubernetes work.

- tested small spot instances successfully
- GPU spot request failed due to service quota (`All G and VT Spot Instance Requests`)
- quota-increase request remained pending for hours

## Azure experience

I was newer to Azure at that time, so some friction may have been user-side.

- too many options and workflow branches for this specific task
- could not quickly reach a clear GPU spot workflow
- did not feel smooth for this weekend experiment

## GCP experience

GCP was the most practical for this use case.

- clearer UI for VM + pricing configuration
- one-click generation of CLI/API/Terraform snippets
- quota request flow was faster in my case
- successful launch of a GPU spot VM after approval

After SSH access, getting Ollama and Open WebUI running was straightforward with Docker.

## Why GCP won for this test

1. Faster path from idea to running GPU VM.
2. Better visibility into pricing while configuring resources.
3. Easier conversion from manual setup to IaC/CLI workflows.

## Final note

This was a practical comparison from one specific setup and time window, not a universal ranking.

My planned next step was to codify equivalent Terraform flows for all three clouds so experimentation can stay reproducible and vendor-neutral.
