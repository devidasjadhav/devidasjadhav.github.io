---
title: "Building Localable: A GitHub Pages AI App Builder"
date: 2026-02-13T10:00:00+05:30
slug: "building-localable-github-pages-ai-app-builder"
description: "How I built a browser-only AI app builder with zero backend and free hosting on GitHub Pages."
tags: ["ai", "javascript", "github-pages", "opensource", "privacy"]
---

Adapted from my LinkedIn article: [Building Localable - A GitHub Pages AI App Builder](https://www.linkedin.com/pulse/building-localable-github-pages-ai-app-builder-devidas-jadhav-s69cf).

I wanted to build an AI app builder that does not require backend infrastructure.

That became **Localable**: a browser-only app builder that you can host for free on GitHub Pages.

- Live app: [devidasjadhav.github.io/bootstrap-lovable](https://devidasjadhav.github.io/bootstrap-lovable/)
- Source: [devidasjadhav/bootstrap-lovable](https://github.com/devidasjadhav/bootstrap-lovable)

## Problem with most app builders

Most tools in this space depend on:

- paid backend servers
- vendor lock-in
- complex deployment setup
- user data flowing through third-party infra

I wanted the opposite: local-first behavior and simple deployment.

## What Localable does

- runs entirely in browser
- works with OpenAI, Gemini, and DeepSeek
- stores keys in-session (cleared on refresh)
- generates Bootstrap + Vanilla JS apps
- requires no backend service

## Build journey highlights

### Week 1: Foundation

I wired streaming API calls and quickly hit callback integration bugs. Early testing saved me from building on broken flow.

### Week 2: Security

I moved from `localStorage` to short-lived session handling and added clearer user warnings. Privacy had to be a first-class feature.

### Week 3: Incremental updates

Initial behavior regenerated the full app for tiny changes (for example, changing a button color). I switched to context-aware diffs so updates became targeted and cheaper.

### Week 4: UX polish

Dark-theme inconsistencies and panel usability were rough. I added resize handles, collapsible areas, and better loading feedback.

## Current capabilities

- multi-provider model access
- streaming responses
- sandboxed live preview
- project persistence with IndexedDB
- ZIP export

## Key takeaway

For this class of tool, static architecture is a feature, not a limitation. Browser-only + GitHub Pages can be enough for powerful developer workflows.
