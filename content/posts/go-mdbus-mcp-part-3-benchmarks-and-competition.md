---
title: "go-mdbus-mcp Part 3: Benchmark Results and Competitive Comparison"
date: 2026-04-11T21:30:00+05:30
slug: "go-mdbus-mcp-part-3-benchmarks-and-competition"
description: "How go-mdbus-mcp was benchmarked internally and compared externally against other Modbus MCP servers on a shared backend."
cover: "https://images.unsplash.com/photo-1516117172878-fd2c41f4a759?auto=format&fit=crop&w=1600&q=80"
coverCredit: "Photo by Unsplash"
tags: ["benchmark", "mcp", "modbus", "performance"]
series: "go-mdbus-mcp Build Story"
---

Part 1 covered architecture, Part 2 covered the journey. This final part answers the uncomfortable question every project eventually faces: “Great story, but does it actually hold up under load?”

## How I approached testing (after a few false starts)

Early on, it was tempting to run a few happy-path checks and call it done. That was not enough.

So the test model became staged:

1. verify protocol and core tool behavior,
2. verify policy and negative paths,
3. run stress lanes and collect latency/error trends,
4. run the same backend against other servers for context.

The key change was mindset: stop asking “does it work once?” and start asking “does it behave consistently?”

## What internal runs showed

Across `stdio`, `sse`, and `streamable`, the staged suites were stable with both drivers.

The interesting part was not a single winner. It was that the winner changed depending on workload and transport profile:

- in one lane, `simonvetter` edged ahead,
- in another, `goburrow` did better.

That was a useful reminder not to hardcode assumptions into defaults.

## External comparison: useful, but with context

I compared against other Modbus MCP servers on the same backend (`mbserver` on `127.0.0.1:5002`) to avoid hand-wavy claims.

| Server | RPS (conc=1) | RPS (conc=5) | p95 ms (conc=5) |
| --- | ---: | ---: | ---: |
| go-mdbus-mcp | 753.9 | 804.9 | 6.96 |
| alejoseb/ModbusMCP | 227.7 | 781.4 | 6.38 |
| kukapay/modbus-mcp | 127.5 | 437.3 | 8.03 |
| midhunxavier/MODBUS-MCP | 141.1 | 426.4 | 8.12 |
| ezhuk/modbus-mcp | 9.4 | 45.1 | 115.92 |

On this setup, `go-mdbus-mcp` came out strong in throughput. But the bigger takeaway is that comparison is now reproducible. Anyone can challenge or re-run it with a different backend and see where results shift.

## What I would not claim from these numbers

I would not claim universal superiority. That is not how this domain works.

Different devices, register maps, network quality, and runtime flags can change results quickly. Throughput is one axis; p95/p99 behavior and operational safety are just as important.

## What I do feel confident claiming

This server now has a better foundation than a demo project:

- predictable test stages,
- measurable behavior,
- policy-aware writes,
- and enough transport flexibility to run in both local and remote MCP workflows.

That combination matters more than winning one benchmark chart.

## Closing the series

If this three-part series had one theme, it is this: the real work is not adding tools fast. The real work is making behavior understandable and repeatable.

That is the difference between “interesting prototype” and “something I can trust in production-like conditions.”
