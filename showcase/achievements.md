# Achievements

## Project 1: MAOps Linux DevOps Toolkit

**Status: stable `v1.0.0` release.**

### Problem Statement

Most "learn Bash scripting" projects stop at a handful of standalone,
disconnected scripts. This project asks what it takes to bring
production-inspired engineering discipline — modularity, input
validation, deterministic testing, supply-chain-aware packaging, CI
enforcement — to a Linux diagnostics/reporting toolkit, without ever
requiring `sudo`, a package manager, or a runtime language beyond Bash.

### Solution Summary

A single, consistent CLI (`maops <group> <command>`) unifying system,
monitoring, filesystem, network, user, process, and service diagnostics
behind one entry point, plus persistent configuration, a `doctor`
health check, tamper-evident `integrity` verification, and an
operational `report` command (text/JSON, with redaction). Every command
is safe to run unattended: read-only by default, `set -euo pipefail`
throughout, no destructive defaults.

### Architecture Summary

`bin/maops` is a thin dispatcher: it resolves its own symlink chain,
sources a fixed-order bootstrap (`colors.sh` → `config.sh` → `helpers.sh`
→ `logger.sh` → `output.sh` → `cli.sh`), and `exec`s directly into the
matching leaf script — so the leaf script's own exit code becomes the
CLI's exit code, with no wrapper subshell in between. Every leaf script
shares the same common libraries instead of reimplementing logging,
argument validation, or JSON assembly independently.

### Primary Technologies

Bash 5.x · Linux (Ubuntu / WSL2 Ubuntu) · Git · GitHub Actions ·
ShellCheck · Bats (bats-core)

### Security and Integrity Highlights

- **Read-only by default.** Only four code paths intentionally mutate
  anything: `install.sh`/`uninstall.sh`, `config init`, and `report save`.
- **No `eval`, no `jq`, no sourced configuration.** JSON is assembled by
  hand with proper escaping; config files are parsed line-by-line, never
  `source`d, so a malicious config file cannot execute arbitrary code.
- **Two-tier archive integrity** — an external `.sha256` proves
  archive-level byte fidelity, and an internal `MAOPS-MANIFEST.tsv`
  independently proves per-file content and mode. Neither proves
  publisher identity, and that boundary is documented explicitly rather
  than glossed over.
- **Redaction by design.** `report --redact` overwrites identifying
  fields (hostname, config path) before rendering, with a regression
  test asserting no `$HOME`/repo-path/IP leak survives it.
- **File modes derived from Git's index, never filesystem `stat`** —
  the fix for a real WSL/drvfs bug where every file on a Windows-mounted
  filesystem reports mode `0777` regardless of what Git actually tracks.

### CI/CD Highlights

A single GitHub Actions workflow (`Bash Validation`) runs on every push
and pull request to `main`: checkout pinned to a full commit SHA
(enforced by its own regression test), install `shellcheck`/`bats`/
`python3`, then `make final-check` — syntax validation, ShellCheck,
executable-mode enforcement, the full Bats suite, package build and
verification, install/uninstall smoke test, documentation validation,
example validation, and a final JSON-report/integrity sanity pass — all
under a redirected temporary `$HOME`. `contents: read` is the only
permission granted; nothing in the pipeline can publish, tag, or write
back to the repository.

### Final Automated Test Count

**529/529 tests passing, 0 failures** — per the Day 8 v1.0.0 final
release-readiness review (`make clean final-check`):

- 517 core Bats tests across 20 test files (`tests/*.bats`, one per
  `scripts/` module), all offline and independent of the host's real
  system state.
- 12 example tests (`tests/examples/examples.bats`), run separately via
  `make examples-check`.

### Release-Package Details

- `dist/maops-linux-devops-toolkit-1.0.0.tar.gz` + `.sha256` checksum,
  built reproducibly (`tar --sort=name --mtime=@0 --owner=0 --group=0
  --numeric-owner`, `gzip -n`) so the same source tree always produces
  byte-identical output.
- Release contents are driven by one shared array
  (`RELEASE_FILE_LIST`), used by both `install.sh` and `package.sh`, so
  the installed runtime tree and the release tarball can never drift
  apart.
- Only Git-tracked files are staged, with every file's permission mode
  taken from Git's index rather than the source filesystem's own `stat`.

### Key Engineering Lessons

- **Trust the Git index, not the filesystem** whenever a file's
  permission mode matters for security — a lesson that generalizes well
  beyond WSL to any environment where `stat` and version control can
  legitimately disagree.
- **A test that requires a specific real environment is a test that
  won't run in CI.** Every environment-specific bug found (drvfs
  permissions, BusyBox-shaped command output, missing optional commands)
  became a synthetic, reproducible fixture instead of a "works on my
  machine" assumption.
- **Diagnostic commands should report health, not silently coerce it.**
  `report`'s exit code reflects the actual pass/warn/fail verdict it
  found, not merely whether the command ran.
- **A single source of truth beats keeping two things in sync by
  discipline.** `RELEASE_FILE_LIST`, the project version, and the shared
  bootstrap load order all exist specifically to remove a category of
  "I forgot to update the other place" bug.

### Screenshots

See [showcase/screenshots.md](screenshots.md).

### Links

- Repository: https://github.com/raiyan10/maops-linux-devops-toolkit
- v1.0.0 Release: https://github.com/raiyan10/maops-linux-devops-toolkit/releases/tag/v1.0.0
- Portfolio Case Study: https://github.com/raiyan10/maops-linux-devops-toolkit/blob/main/docs/portfolio-case-study.md

---

## Project 2: MAOps Python DevOps Automation Toolkit

**Status: stable `v0.7.0` release — the final planned release of the
project's seven-day portfolio arc.**

### Problem Statement

DevOps and platform teams routinely need small, trustworthy diagnostic
tools: what does the environment look like, is this endpoint up, what
does a log actually say happened, did last night's checks all pass. Such
tools are usually either shell scripts (fast to write, hard to keep safe
and typed as they grow) or heavyweight frameworks (safe, but overkill for
a diagnostics CLI).

### Solution Summary

A dependency-free Python CLI (`maops-py`) exploring a third point: a
small, strictly typed CLI that treats its own security boundaries — no
shell, no arbitrary command execution, a narrowly scoped network surface
— as first-class design constraints from day one. It unifies environment
diagnostics (`doctor`), typed TOML configuration, allowlisted subprocess
tool inspection, system/filesystem inventory, structured log parsing and
analysis with default secret redaction, bounded HTTP/TCP health checks,
aggregated multi-report summarization, and declarative TOML automation
workflows, behind one consistent command surface.

### Architecture Summary

An `argparse`-based CLI dispatches to one `commands/*.py` orchestration
function per subcommand, each composing typed, frozen-dataclass models
from `core/*.py` and rendering them through one shared text/JSON/Markdown
output layer. Two narrow, explicit exceptions to "no subprocess, no
network" — a fixed five-tool subprocess allowlist in `core/runner.py`,
and a fixed HTTP/TCP surface in `core/health_http.py`/`core/health_tcp.py`
— are each isolated to a single module and enforced by dedicated
architectural regression tests, not only code review.

### Primary Technologies

Python 3.11+ (CI matrix through 3.14) · standard library only
(`argparse`, `tomllib`, `http.client`, `ssl`, `socket`,
`concurrent.futures`) · pytest · ruff · mypy `--strict` · GitHub Actions.

### Security and Integrity Highlights

- **No shell, no arbitrary command execution.** No `shell=True`,
  `os.system`, `eval`, `exec`, or `pickle` anywhere in `src/`.
  `core/runner.py` is the only module permitted to import `subprocess`,
  and only with one of five fixed, hardcoded argv tuples.
- **The workflow file is data, not code.** `workflow run` dispatches a
  fixed, closed set of seven step kinds to the package's own existing
  report-building functions — never a shell command, `eval`, or template
  engine — proven by a dedicated test that feeds real shell-metacharacter
  payloads through a canary-file-creation attempt and asserts the file is
  never created.
- **A two-module-wide network surface.** `core/health_http.py` and
  `core/health_tcp.py` are the only modules permitted to import
  `socket`/`ssl`/`http.client`; every other module's absence of network
  access is enforced by a static import-boundary regression test. HTTPS
  always validates certificates and hostnames, with no `--insecure` flag.
- **Fd-safe, symlink-refusing file reads and atomic, symlink-race-proof
  writes** across every module that reads file content or writes output.
- **Zero third-party runtime dependencies** across all seven releases.

### CI/CD Highlights

A single `Python Validation` GitHub Actions workflow, SHA-pinned actions,
running a Python 3.11/3.12/3.13/3.14 matrix. Every release runs `make
quality` (format-check, lint, `mypy --strict`, coverage) → `make build` →
`make smoke-install` (an isolated, offline install of the **exact built
wheel**, `PIP_NO_INDEX=1 --no-deps`) → `make release-check`, before a tag
and GitHub Release.

### Final Automated Test Count

**1323/1323 tests passing, 0 failures, 0 skipped. Coverage: 98.49%**
(floor 90%) — per the Day 7 v0.7.0 final release-readiness review (`make
quality`).

### Release-Package Details

- `maops_pydevops-0.7.0-py3-none-any.whl` + `maops_pydevops-0.7.0.tar.gz`.
- `make smoke-install` installs the **exact built wheel** — never
  editable source, never a fresh PyPI resolve — into an isolated,
  offline temporary venv, so a release is validated against the actual
  artifact a user would receive.
- `src`-layout packaging; zero runtime dependencies declared in
  `pyproject.toml`.

### Key Engineering Lessons

- **Coverage is a floor, not a proof.** The project's own Day 6 test
  review documents a case where two real defects sat on 99%-covered
  lines and were only caught by pointing hostile input at the specific
  field that mattered, not by the coverage percentage.
- **"Data, not code" has to be a structural fact, not a policy.** The
  declarative workflow format has no templating, `eval`, or shell
  interpolation path to begin with, rather than relying on input
  sanitization to make an executable format safe.
- **Structural detection beats heuristic guessing.** `report aggregate`
  requires a fixed, unique JSON key combination per supported report
  kind rather than a best-effort schema sniff — a document that doesn't
  structurally match any supported kind is rejected outright.
- **An independent review-and-remediation loop closes what it defers.**
  Specialist review documents per day, with follow-up documents proving
  Medium/Low findings deferred at release time were actually closed in
  a later pass rather than silently dropped.

### Screenshots

See [showcase/screenshots.md](screenshots.md).

### Links

- Repository: https://github.com/raiyan10/maops-python-devops
- v0.7.0 Release: https://github.com/raiyan10/maops-python-devops/releases/tag/v0.7.0
- Portfolio Guide: https://github.com/raiyan10/maops-python-devops/blob/main/docs/portfolio-guide.md

---

## Project 3: MAOps Docker Platform

**Status: stable `v1.0.0` release.**

### Problem Statement

Most "learn Docker" projects stop at a working `Dockerfile` and a
`docker-compose up`. This project asks what it takes to bring
production-inspired container engineering discipline — non-root
execution, capability dropping, read-only filesystems, network
segmentation, reproducible builds, and supply-chain-verified releases —
to a small multi-service platform, with the application layer kept
deliberately trivial so nearly all of the engineering effort is the
container layer itself.

### Solution Summary

A secure, minimal Docker/Compose platform foundation: three runtime
services (`gateway -> app -> state`) built from one shared, hardened
Distroless Python image, communicating over a segmented edge/internal
network topology with a persistent state volume and gateway-only
loopback host publication. The Python application in `app/` is
intentionally tiny — a few JSON endpoints — so it exists only as a
deterministic workload for demonstrating real Docker/container
engineering practices.

### Architecture Summary

Three services share one image built through a multi-stage Dockerfile
onto a digest-pinned Distroless Python base. The `gateway` service is
the platform's only host-published entry point; `app` and `state` are
reachable solely over an internal backend network, with a separate edge
network isolating the gateway. State is persisted through a dedicated
`state_data` volume rather than any service's own writable layer.

### Primary Technologies

Docker · Docker Compose · Python (stdlib-only application, Distroless
runtime) · GitHub Actions · Trivy · SPDX

### Security and Integrity Highlights

- **Non-root, capability-dropped, read-only runtime.** Every container
  runs as UID:GID `10001:10001` on a read-only root filesystem, with
  `cap_drop: ALL` and `no-new-privileges`, and no Docker socket mounted
  into any workload or scanner container.
- **Digest-pinned bases with a controlled security overlay.** All base
  images are pinned by digest; a narrowly scoped Debian security overlay
  patches an emergency `libssl3t64` finding, with its own automated
  lifecycle tripwire that detects when a future base refresh makes the
  overlay redundant.
- **Network segmentation by default.** An edge network fronts only the
  gateway; an internal backend network carries `gateway -> app -> state`
  traffic; only the gateway is published to the host, and only on
  loopback.
- **Supply-chain-verified release.** An SPDX SBOM and a pinned Trivy scan
  ship with every release; the `v1.0.0` release policy required 0
  Critical and 0 fixable High findings (unfixed Highs left visible and
  non-blocking) — a point-in-time result, since vulnerability databases
  are time-varying, not a permanently fixed count.

### CI/CD Highlights

A GitHub Actions pipeline runs `make quality` and the full `make
release-check` (build, inspect, image-audit, smoke, security-check,
compose-test, reliability-check, reproducibility-check,
supply-chain-check, patch-lifecycle-check, release-bundle) on every push
and pull request. A separate release workflow offers a safe,
non-publishing dry run alongside a controlled, tag-triggered GitHub
Release publication, with least-privilege, per-job permissions and every
action pinned to an immutable commit SHA.

### Final Automated Test Count

**688 automated unit tests passing** after the Day 7 remediation pass,
alongside a **32/32-check reliability suite run against real Docker**
covering health/readiness separation, resource limits, bounded
`on-failure:3` restarts, graceful shutdown, persistence validation, and
both transient-OOM automatic recovery and persistent-OOM
restart-exhaustion/operator-recovery paths.

### Release-Package Details

- Strong reproducibility evidence: rebuilding the image produces an
  exact image-ID match against the released artifact.
- Release assets: SPDX SBOM (`maops-docker-platform-1.0.0.spdx.json`),
  pinned Trivy scan report (`trivy-1.0.0.json`), and `SHA256SUMS`, staged
  as a flat, basename-only bundle.
- Real downloaded-release consumer verification: a fresh
  `sha256sum -c SHA256SUMS` run against the actual published GitHub
  Release assets passed.
- An annotated `v1.0.0` Git tag backs the published GitHub Release.

### Key Engineering Lessons

- **A flat release bundle is what a real consumer actually verifies
  against.** An earlier `v0.6.0` release shipped a `SHA256SUMS` file
  referencing CI-internal paths that a normal flat download couldn't
  check — fixed by staging and independently verifying a basename-only
  bundle before every subsequent release.
- **A security overlay needs its own exit condition, not just its own
  justification.** The `libssl3t64` Debian-security patch ships with an
  automated tripwire that independently pulls the real pinned base and
  proves whether the overlay is still required, now redundant, or has
  drifted — rather than trusting a comment to stay accurate.
- **Vulnerability-scan results are a snapshot, not a guarantee.** The
  release policy's "0 Critical, 0 fixable High" result reflects the
  scanner database at release time; it is documented as time-varying
  rather than claimed as a permanent property of the image.
- **An independent review-and-remediation loop closes what it defers,
  the same discipline as Projects 1 and 2.** Every still-relevant
  Low/Medium finding from Days 1-6's engineering reviews was reviewed and
  explicitly adjudicated — closed, accepted, or still open — in the Day 7
  final pass rather than silently dropped.

### Screenshots

See [showcase/screenshots.md](screenshots.md).

### Links

- Repository: https://github.com/raiyan10/maops-docker-platform
- v1.0.0 Release: https://github.com/raiyan10/maops-docker-platform/releases/tag/v1.0.0
- Production Readiness: https://github.com/raiyan10/maops-docker-platform/blob/main/docs/production-readiness.md

---

No production users, revenue, uptime, or business-impact figures are
claimed for any of the three projects — all are portfolio and
engineering-practice projects, not deployed services.
