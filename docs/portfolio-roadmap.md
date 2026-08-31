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

## ✅ MAOps Python DevOps Automation Toolkit — stable `v0.7.0`

Repository: https://github.com/raiyan10/maops-python-devops
Release: https://github.com/raiyan10/maops-python-devops/releases/tag/v0.7.0

What shipped in v0.7.0:

- A dependency-free `maops-py` CLI (`doctor`, `config`, `tools inspect`,
  `inventory system`/`filesystem`, `logs parse`/`analyze`, `health http`/
  `tcp`, `report aggregate`, `workflow validate`/`run`), built as seven
  scoped, tagged daily releases (`v0.1.0`-`v0.7.0`) — the planned
  seven-day development cycle is complete, with v0.7.0 as its final
  planned release.
- 1323/1323 automated tests passing, 98.49% coverage (floor 90%), per the
  Day 7 v0.7.0 final release-readiness review.
- Zero third-party runtime dependencies across all seven releases;
  architecturally enforced security boundaries (no shell/`eval`/`exec`,
  a two-module-wide network surface with mandatory TLS verification, a
  "workflow file is data, not code" guarantee, fd-safe symlink-refusing
  file reads, atomic symlink-race-proof writes).
- Exact-wheel offline smoke installation (`make smoke-install`) and a
  Python 3.11-3.14 GitHub Actions CI matrix on SHA-pinned actions.
- Full v0.7.0 documentation set (architecture, portfolio guide, security
  policy, release process, per-feature safety docs).

See [showcase/achievements.md](../showcase/achievements.md) for the full
Project 2 write-up.

## ✅ MAOps Docker Platform — stable `v1.0.0`

Repository: https://github.com/raiyan10/maops-docker-platform
Release: https://github.com/raiyan10/maops-docker-platform/releases/tag/v1.0.0

What shipped in v1.0.0:

- A three-service `gateway -> app -> state` platform built from one
  shared image, running on an edge network (gateway-only, loopback host
  publication) plus an internal backend network, with a persistent
  `state_data` volume.
- A hardened runtime end-to-end: multi-stage build onto a digest-pinned
  Distroless Python base, non-root `10001:10001`, read-only root
  filesystem, `cap_drop: ALL`, `no-new-privileges`, no Docker socket
  mounted into any workload/scanner container, and a controlled Debian
  security overlay with its own lifecycle tripwire.
- A 32/32-check reliability suite against real Docker (health/readiness
  separation, resource limits, bounded `on-failure:3` restarts, graceful
  shutdown, persistence validation, and both transient-OOM automatic
  recovery and persistent-OOM restart-exhaustion/operator-recovery
  paths).
- 688 automated unit tests passing after the Day 7 remediation pass.
- A supply-chain-verified release: strong reproducibility evidence
  (exact image-ID equality across rebuilds), an SPDX SBOM, pinned Trivy
  scanning (0 Critical, 0 fixable High at release time — unfixed Highs
  left visible and non-blocking; vulnerability databases are
  time-varying, so this is a point-in-time result, not a permanent
  guarantee), a GitHub Actions CI pipeline, a secure dry-run-vs-real tag
  release flow, an annotated `v1.0.0` tag, a flat release bundle, and a
  real downloaded-release consumer check (`sha256sum -c SHA256SUMS` ->
  PASS).

See [showcase/achievements.md](../showcase/achievements.md) for the full
Project 3 write-up.

## 🚧 Remaining portfolio repositories

All other repositories listed in the root `README.md` roadmap table
remain in planning/early stages — Project 4 (Kubernetes Platform) is
next — and this document will be updated as each one reaches a stable
release.
