---
title: "Secure Container Image Builds with GitHub Actions"
date: 2026-04-07
slug: "secure-image-builds-github-actions"
description: "A minimal GitHub Actions approach to reproducible builds, provenance, and vulnerability checks for container images."
cover: "https://images.unsplash.com/photo-1516117172878-fd2c41f4a759?auto=format&fit=crop&w=1600&q=80"
coverCredit: "Photo by Unsplash"
tags: ["github-actions", "ci-cd", "supply-chain"]
---

GitHub Actions can produce trustworthy images when the pipeline is explicit about provenance.

## Baseline workflow

1. Build with pinned actions and deterministic build arguments.
2. Generate an SBOM for every pushed image digest.
3. Run vulnerability scanning as a required check.
4. Sign images using keyless OIDC signing.

## Team-level win

Once this pattern is in place, deployment approvals can focus on actual risk instead of guessing whether an image is trustworthy.
