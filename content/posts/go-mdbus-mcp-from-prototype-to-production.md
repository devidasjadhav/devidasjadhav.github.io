---
title: "go-mdbus-mcp Part 1: Why This Stack and Architecture"
date: 2026-04-08T21:10:00+05:30
slug: "go-mdbus-mcp-part-1-architecture"
description: "Why go-mdbus-mcp was built with Go + official MCP SDK, and how the runtime architecture is designed for industrial reliability."
cover: "https://images.unsplash.com/photo-1498050108023-c5249f4df085?auto=format&fit=crop&w=1600&q=80"
coverCredit: "Photo by Unsplash"
tags: ["golang", "mcp", "modbus", "architecture"]
series: "go-mdbus-mcp Build Story"
---

I started [go-mdbus-mcp](https://github.com/devidasjadhav/go-mdbus-mcp) for a practical reason: I wanted AI tools to interact with industrial data without adding another fragile middleware layer.

This is Part 1 of the series, and it is the “why this shape?” part.

## Where this started

When you work around PLC integrations long enough, you keep seeing the same pattern.

One team has a script that only one person understands. Another has a service that works until a timeout edge appears in production. A third has clean tooling, but it is too heavy to run comfortably near the actual plant network.

I wanted something smaller and calmer:

- one binary,
- explicit behavior,
- safe defaults,
- and no drama when network quality is bad.

## Why Go felt like the right default

This was not a language-war decision. It was an operations decision.

In environments where machines run for long periods and upgrades are conservative, a single static binary removes many classes of surprise. No runtime drift, no pip/npm dependency roulette, no “works on one host but not another” because of package resolution.

That gave me a stable base. From there, I moved the MCP layer to the official Go SDK so protocol behavior was less custom and more standard.

## The architectural choices that reduced future pain

I tried to keep the runtime boring on purpose:

1. parse config and flags,
2. load write policy,
3. load tags,
4. start Modbus client,
5. register tools,
6. serve transport.

The boring part is good. It means you can reason about failures quickly.

A few choices turned out to be especially valuable:

- **Writes are opt-in.** You should not be able to mutate field values accidentally.
- **Policy is explicit.** Address allowlists and write gates live in config/env, not hidden inside handler code.
- **Mock mode exists from day one.** CI and local validation do not depend on hardware availability.
- **Tag mapping is CSV-first.** Humans can review and maintain semantic points outside code.

## A quick view of module boundaries

The project gradually settled into clear boundaries:

- runtime/bootstrap in `main.go` and `internal/mcpserver`
- config loading/precedence in `internal/config`
- driver and retry behavior in `modbus/client*.go`
- write safety in `modbus/write_policy.go`
- semantic layer in `modbus/tag_map.go` and `modbus/tag_codec.go`
- MCP handlers in `modbus/tool_*.go`

What I like about this split is simple: transport concerns and Modbus concerns do not leak into each other too much.

## The real objective behind all of this

The goal was never “have the longest feature list.”
The goal was “make production behavior predictable.”

If an operator asks, “What happens if the connection drops?” there should be a clear answer.
If someone asks, “Can this accidentally write to the wrong range?” there should be a clear policy.

That was the north star for architecture decisions.

In Part 2, I cover how that architecture evolved stage by stage during the build journey.

Next: [go-mdbus-mcp Part 2: Git History as an Engineering Timeline](/posts/go-mdbus-mcp-part-2-git-history/)
