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

## ✅ MAOps Kubernetes Platform — released `v1.0.0`

Repository: https://github.com/raiyan10/maops-kubernetes-platform
Release: https://github.com/raiyan10/maops-kubernetes-platform/releases/tag/v1.0.0

Released on 2026-10-05 at `4d74cfbdeca4bdc56ddc4a207508393f7d4ed438`:

- Gateway/app/state architecture with PVC-backed state and bounded
  persistence/restoration checks.
- Scoped identities, RBAC, Cilium NetworkPolicy, Helm, Gateway API and
  Istio ambient mesh.
- Recreate, Blue/Green and Canary demonstrations.
- Isolated HPA, VPA Off/Initial and KEDA demonstrations, with quota/limit
  governance and verified cleanup.
- Run I passed HPA 9/9, VPA 17/17, KEDA 13/13 (60/60 items) and cleanup
  16/16. The final cluster-free suite passed 1,838 unit tests.

This is a single-host Kind reference platform. Application autoscaling,
state HA and cluster-loss recovery are not established. The VPA
cold-start floor case is test-covered; run I was not a cold start.

The post-release record, two screenshots and a portfolio case study were
published afterwards on the project's `main` in documentation commit
`0bc6de2`, and CI passed on that commit. The `v1.0.0` tag still points to
the release commit above; the documentation commit does not change the
release contents. See the
[post-release verification record](https://github.com/raiyan10/maops-kubernetes-platform/blob/0bc6de2b2e9897ce6b0f158c66e15e6b33dd83d9/docs/engineering-reviews/day-08-post-release-verification.md)
and the [portfolio case study](https://github.com/raiyan10/maops-kubernetes-platform/blob/0bc6de2b2e9897ce6b0f158c66e15e6b33dd83d9/docs/portfolio-case-study.md).

See [showcase/achievements.md](../showcase/achievements.md) for the full
Project 4 write-up.

## Consolidated project sequence

| # | Project | Status |
|---|---|---|
| 1 | Linux Automation Toolkit | Released — v1.0.0 |
| 2 | Python Automation | Released — v0.7.0 |
| 3 | Docker Platform | Released — v1.0.0 |
| 4 | Kubernetes Platform | Released — v1.0.0 (local Kind reference platform) |
| 5 | GitHub Actions CI/CD Platform | Next — architecture first |
| 6 | Terraform AWS Infrastructure Platform | Planned |
| 7 | Ansible Configuration & Automation Platform | Planned |
| 8 | DevSecOps Platform | Planned |
| 9 | Argo CD GitOps Platform | Planned |
| 10 | Observability & AIOps Platform | Planned — SRE perspective |
| 11 | RAG & LLMOps Platform | Planned |
| 12 | MLOps & AI Infrastructure Platform | Planned — equal depth for MLOps, infrastructure and inference |
| 13 | AI Agents, Agentic Workflows, Orchestration & AgentOps Platform | Planned |
| 14 | Enterprise Platform Engineering Capstone — Internal Developer Portal | Planned — integrates P1–13 |

## Reuse obligations

- P5 supplies reusable workflows, artifact identities and delivery-event
  evidence to P14, plus one isolated Tekton comparison exercise. It
  supports future model-serving artifacts without requiring P12 models now.
- P10 provides SRE-oriented telemetry, objectives, alerts and incident
  evidence, including a future inference-observability contract.
- P12 balances model lifecycle/MLOps, reproducible AI infrastructure and
  measured inference engineering; all three require practical evidence.
- P13 delivers bounded, evaluated agent workflows and AgentOps.
- P14 integrates selected prior outputs into an Internal Developer Portal.

The canonical planning record also maps the public CNPA/CNPE topics;
these are coverage obligations, not certification claims. Terraform is
AWS-only in this portfolio. Terraform Azure, Kafka, FDE and AI FDE are
outside this backlog. Sessions are bounded milestones, not fixed calendar
deadlines. Architecture explanation precedes implementation.
