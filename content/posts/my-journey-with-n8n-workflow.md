---
title: "My Journey with n8n Workflow Has Started"
date: 2025-10-12T10:00:00+05:30
slug: "my-journey-with-n8n-workflow"
description: "Setting up a self-hosted n8n workflow with Ollama and WhatsApp integration on a home lab environment."
tags: ["n8n", "automation", "ollama", "self-hosted", "ai"]
---

Adapted from my LinkedIn article: [My journey with n8n workflow has started.](https://www.linkedin.com/pulse/my-journey-n8n-workflow-has-started-devidas-jadhav-fxcjf).

I experimented with many AI tools, but most did not stick in day-to-day use. n8n clicked because it solves a practical problem: event-driven automation with low friction.

## Why n8n

n8n is not just "an AI tool." It is a workflow automation engine.

In simple terms:

- an event arrives (message, webhook, file, API)
- a workflow runs
- output is generated and can trigger another workflow

My first use case was a WhatsApp assistant backed by local Ollama models.

## Lab setup

- one spare PC running n8n
- one laptop with GPU for Ollama
- Docker-based deployment for n8n

Ollama installation was straightforward. n8n setup via Docker quickstart was also simple.

## Real challenge: public webhook endpoint

WhatsApp webhooks require a reachable HTTPS endpoint. My home setup had no static public IPv4.

I evaluated:

1. Cloudflare Tunnel
2. ngrok
3. DuckDNS + Let's Encrypt

I chose the DNS + certificate route because it was scriptable and repeatable for future instances.

## Integration notes

Meta setup was the trickiest part: receive flow and send flow use different IDs/tokens. After baseline echo tests, I connected n8n AI node + Ollama and added memory support.

One useful expression pattern in n8n:

```text
{{ $json.messages[0].text.body }}
```

This avoided hardcoding and passed message content across nodes dynamically.

## Next steps I planned

- scale the setup to cloud
- automate replication across n8n instances
- selectively move credentials while exporting/importing workflows

## Takeaway

n8n gave me a practical bridge between local AI models and real messaging workflows. It turned experimentation into something operational.
