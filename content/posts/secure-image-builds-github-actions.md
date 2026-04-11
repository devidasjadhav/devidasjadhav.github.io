---
title: "Secure Container Image Builds with GitHub Actions"
date: 2026-04-07
slug: "secure-image-builds-github-actions"
description: "A minimal GitHub Actions approach to reproducible builds, provenance, and vulnerability checks for container images."
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
