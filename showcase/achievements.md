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

No production users, revenue, uptime, or business-impact figures are
claimed for this project — it is a portfolio and engineering-practice
project, not a deployed service.
