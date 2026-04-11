---
title: "The Self-Evolving Reviewer"
date: 2026-04-04T10:00:00+05:30
slug: "self-evolving-reviewer"
description: "A weekend experiment in recursive AI code review, where the agent audits and improves its own implementation."
tags: ["ai", "agents", "code-review", "opensource", "build-in-public"]
---

Adapted from my LinkedIn article: [The "Self-Evolving" Reviewer](https://www.linkedin.com/pulse/self-evolving-reviewer-devidas-jadhav-5wklf).

## Can an AI agent fix itself?

I started this as a practical experiment, not a theory exercise.

After working on ESP32 support with Claude Code, I wanted to move from one-off prompts to a reusable review system. The goal was an agent that can **review, patch, test, and iterate** across different stacks.

That idea became **OpenCode-Review**.

## Dogfooding phase

The hard part was not writing prompts. It was making the system robust when it reviewed its own source code.

What happened in early runs:

1. The loop drifted and lost context.
2. The reviewer failed to identify architectural issues it had introduced.
3. Fixes regressed previous behavior.

After multiple correction rounds, the system started to improve. The key shift was adding tighter context boundaries and explicit quality gates before applying patches.

## The bottleneck: rate limits

Recursive multi-agent review consumes tokens quickly.

When one run includes planning, code diffing, tests, and another review pass, infrastructure limits become the dominant constraint. Progress became less about logic and more about throughput.

## What this taught me

- self-correction is possible, but guardrails are mandatory
- architecture/security gates are non-negotiable for auto-applied fixes
- recursive workflows are only as good as their evaluation checkpoints
- infra access can accelerate or block agent evolution

## Looking for collaborators

I am actively interested in validating this workflow across diverse stacks:

- embedded and firmware repos
- web backends and APIs
- infra/devops codebases

Project repository: [devidasjadhav/opencode-review](https://github.com/devidasjadhav/opencode-review)
