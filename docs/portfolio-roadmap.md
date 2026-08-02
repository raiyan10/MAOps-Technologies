# Portfolio Roadmap

Tracks the delivery state of each repository in the MAOps Technologies
portfolio, in more detail than the summary table in the root `README.md`.

## ✅ MAOps Linux DevOps Toolkit — stable `v1.0.0`

Repository: https://github.com/raiyan10/maops-linux-devops-toolkit
Release: https://github.com/raiyan10/maops-linux-devops-toolkit/releases/tag/v1.0.0

What shipped in v1.0.0:

- Unified `maops` CLI dispatcher over system, monitoring, filesystem,
  network, user, process, service, config, `doctor`, `integrity`, and
  `report` commands.
- 529/529 automated tests passing (517 core Bats tests + 12 example
  tests), per the Day 8 v1.0.0 final release-readiness review.
- Two-tier release integrity (external SHA-256 checksum + internal
  per-file manifest) and a reproducible release tarball.
- A single, supply-chain-hardened GitHub Actions workflow (pinned
  checkout SHA, `contents: read` only) gating every push/PR to `main`.
- Full v1 documentation set (architecture, quickstart, compatibility,
  troubleshooting, portfolio case study) and validated examples.

See [showcase/achievements.md](../showcase/achievements.md) for the full
Project 1 write-up.

## 🚧 Remaining portfolio repositories

All other repositories listed in the root `README.md` roadmap table
remain in planning/early stages; this document will be updated as each
one reaches a stable release.
