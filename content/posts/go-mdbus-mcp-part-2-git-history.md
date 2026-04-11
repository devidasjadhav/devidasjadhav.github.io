---
title: "go-mdbus-mcp Part 2: Git History as an Engineering Timeline"
date: 2026-04-09T21:20:00+05:30
slug: "go-mdbus-mcp-part-2-git-history"
description: "A stage-by-stage story of how go-mdbus-mcp moved from a fast prototype to a safer, test-driven release."
cover: "https://images.unsplash.com/photo-1515879218367-8466d910aaa4?auto=format&fit=crop&w=1600&q=80"
coverCredit: "Photo by Unsplash"
tags: ["golang", "mcp", "git", "engineering"]
series: "go-mdbus-mcp Build Story"
---

Part 1 covered stack and architecture. This part tells the build journey as it actually happened.

## Stage 1: make it talk to real devices

The first version was straightforward: read data from Modbus and expose it to an MCP client.

That sounds simple until you run it against real hardware for more than a few minutes. Idle disconnects, timeout edges, and inconsistent field behavior show up immediately. Early work in this stage focused on making read/write paths functionally complete and getting the project into a usable modular shape.

At this point, it was a useful prototype, but still fragile.

## Stage 2: stop treating reliability as an afterthought

The next wave of changes came from operational pain:

- reconnect behavior needed to be explicit,
- default tool inputs needed safer behavior,
- write operations needed policy gates,
- recovery settings needed to be tunable.

Instead of only adding new tools, the project started to define "safe behavior under bad conditions" as a first-class requirement.

This is usually the stage where projects either become trustworthy or stall as demoware.

## Stage 3: move from raw addresses to semantic operations

Once the core was stable, developer and operator ergonomics became the bottleneck.

Raw register addressing works for protocol experts, but it is a poor fit for AI-assisted workflows and team collaboration. The project added semantic tag mapping, CSV-driven definitions, typed reads/writes, and mock mode for no-hardware validation.

That changed day-to-day usage from:

"Read register 40017"

to:

"Read line_pressure"

The difference is not cosmetic. It improves clarity, reviewability, and safety.

## Stage 4: prove behavior, don’t just claim it

After feature depth and reliability work, the question shifted to evidence.

A staged validation model was introduced:

1. protocol and core behavior,
2. policy and negative paths,
3. stress/performance,
4. external comparison against other MCP servers.

This stage turned the project from "looks solid" into "can be evaluated consistently."

## Release progression as maturity markers

The release tags (`v0.0.1` to `v0.0.4`) map to visible maturity shifts:

- from basic functionality,
- to MCP alignment and safer defaults,
- to semantic usability and broader verification,
- to stronger typed-write coverage and competitive validation.

## What this journey taught me

1. Reliability work is not separate from product work; it is product work.
2. Semantic abstractions are not "extra" for industrial systems; they reduce human error.
3. Benchmarks matter only when backed by repeatable methodology.

In Part 3, I cover that benchmarking and external comparison setup in detail.

Next: [go-mdbus-mcp Part 3: Benchmark Results and Competitive Comparison](/posts/go-mdbus-mcp-part-3-benchmarks-and-competition/)
