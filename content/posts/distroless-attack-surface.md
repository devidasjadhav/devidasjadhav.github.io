---
title: "From Dockerfiles to Distroless: Shrinking Container Attack Surface"
date: 2026-04-11
slug: "distroless-attack-surface"
description: "A migration path from general-purpose base images to distroless containers with fewer vulnerabilities and clearer ownership."
cover: "https://images.unsplash.com/photo-1516117172878-fd2c41f4a759?auto=format&fit=crop&w=1600&q=80"
coverCredit: "Photo by Unsplash"
tags: ["docker", "security", "distroless"]
---

Moving to distroless images is less about trend-following and more about reducing unknown risk.

## Why teams delay this change

Most teams keep full Linux base images because they simplify debugging. The tradeoff is a larger package tree, more CVEs, and slower patch cycles.

## Migration pattern that works

1. Keep build tooling in a dedicated builder stage.
2. Copy only the final binary or compiled assets into the runtime image.
3. Run as a non-root user by default.
4. Attach an SBOM and sign the final image in CI.

## Practical compromise

Use distroless in production and maintain a separate debug image variant for emergency investigations.
