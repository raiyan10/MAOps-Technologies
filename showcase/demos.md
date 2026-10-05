# Demos

## Project 1: MAOps Linux DevOps Toolkit

Command examples reflecting the actual `v1.0.0` CLI surface (see the
[toolkit's own README](https://github.com/raiyan10/maops-linux-devops-toolkit#cli-usage)
for the complete list):

```bash
# Version, help, environment health check
maops --version
maops --help
maops doctor

# System, monitoring, filesystem
maops system info
maops monitoring memory
maops filesystem disk

# Network diagnostics
maops network ping example.com 4
maops network dns example.com
maops network port example.com 443 2

# Configuration (CLI arg -> MAOPS_* env var -> config file -> default)
maops config init
maops config show --format json

# Tamper-evident integrity check
maops integrity --format json

# Operational reporting, with redaction and secure atomic save
maops report summary
maops report summary --redact
maops report save --output /tmp/report.json --format json
```

See [docs/quickstart.md](https://github.com/raiyan10/maops-linux-devops-toolkit/blob/main/docs/quickstart.md)
and [docs/demo-workflow.md](https://github.com/raiyan10/maops-linux-devops-toolkit/blob/main/docs/demo-workflow.md)
in the toolkit repository for a full sandboxed walkthrough.

## Project 2: MAOps Python DevOps Automation Toolkit

A representative walkthrough of the `v0.7.0` CLI surface, from first
`--help` to the published release:

1. **CLI capability overview** — `maops-py --help`, surfacing every
   subcommand (`doctor`, `config`, `tools`, `inventory`, `logs`, `health`,
   `report`, `workflow`) from one entry point.
   See [01-cli-overview.png](../images/thumbnails/python-devops-toolkit/01-cli-overview.png).
2. **Aggregated operational report** — `maops-py report aggregate
   doctor.json health.json --format markdown`, normalizing multiple
   `maops-py` JSON reports into one structurally validated summary.
   See [02-aggregated-report.png](../images/thumbnails/python-devops-toolkit/02-aggregated-report.png)
   and [docs/aggregated-reports.md](https://github.com/raiyan10/maops-python-devops/blob/main/docs/aggregated-reports.md).
3. **Declarative workflow execution** — `maops-py workflow run
   release.toml --output report.json`, running a fixed, closed set of
   step kinds sequentially through the package's own internal APIs.
   See [03-declarative-workflow.png](../images/thumbnails/python-devops-toolkit/03-declarative-workflow.png)
   and [docs/workflows.md](https://github.com/raiyan10/maops-python-devops/blob/main/docs/workflows.md).
4. **Full release validation** — `make release-check` (`make quality` →
   `make build` → `make smoke-install`), the same gate every one of the
   seven tagged releases passed before a tag was cut.
   See [04-release-check.png](../images/thumbnails/python-devops-toolkit/04-release-check.png)
   and [docs/release-process.md](https://github.com/raiyan10/maops-python-devops/blob/main/docs/release-process.md).
5. **Published `v0.7.0` release** — the final planned release of the
   project's seven-day portfolio arc, tagged and published on GitHub.
   See [05-v0.7.0-release.png](../images/thumbnails/python-devops-toolkit/05-v0.7.0-release.png)
   and the [v0.7.0 release page](https://github.com/raiyan10/maops-python-devops/releases/tag/v0.7.0).

```bash
maops-py --help
maops-py report aggregate doctor.json health.json --format markdown
maops-py workflow run release.toml --output report.json
make release-check
```

See [docs/portfolio-guide.md](https://github.com/raiyan10/maops-python-devops/blob/main/docs/portfolio-guide.md)
in the toolkit repository for the full project narrative.

## Project 3: MAOps Docker Platform

A representative walkthrough of the `v1.0.0` release-validation surface,
from CI on `main` to a verified published release:

1. **Continuous integration** — every push/PR to `main` runs `make
   quality` then the full `make release-check`.
   See [03-main-ci-green.png](../images/thumbnails/docker-platform/03-main-ci-green.png).
2. **Tag-triggered release run** — pushing the `v1.0.0` tag runs the
   release workflow's `Validate` (release-policy gates, tag/version/
   history checks) and `Publish GitHub Release` jobs.
   See [06-v100-tag-release-run.png](../images/thumbnails/docker-platform/06-v100-tag-release-run.png).
3. **Published `v1.0.0` GitHub Release**, with its SPDX SBOM, pinned
   Trivy scan report, and `SHA256SUMS` attached as release assets.
   See [07-v100-github-release.png](../images/thumbnails/docker-platform/07-v100-github-release.png)
   and [08-v100-release-assets.png](../images/thumbnails/docker-platform/08-v100-release-assets.png).
4. **Real downloaded-release consumer verification** — `sha256sum -c
   SHA256SUMS` run against the actual downloaded release assets, not
   the CI build artifacts.
   See [10-v100-consumer-verification.png](../images/thumbnails/docker-platform/10-v100-consumer-verification.png).

```bash
make quality
make release-check
docker compose up
```

See [docs/production-readiness.md](https://github.com/raiyan10/maops-docker-platform/blob/main/docs/production-readiness.md)
in the platform repository for the full project narrative.

## Project 4: MAOps Kubernetes Platform

Start with the [v1.0.0 release](https://github.com/raiyan10/maops-kubernetes-platform/releases/tag/v1.0.0)
and the project's [README](https://github.com/raiyan10/maops-kubernetes-platform#day-8-status-released-v100-local-kind-reference-platform).
The recorded demonstrations cover state persistence, identity/network
boundaries, deployment strategies and isolated autoscaling.

The project's `make day8-plan` prints the live sequence without running it;
`make day8-static-check` checks the design without cluster access.
`make day8-check` mutates its scoped local environment and is an optional
reproduction step requiring the documented preflight, resource budget
and existing Day 7 cluster. It is not necessary just to browse evidence.

Run I's published results are HPA 9/9, VPA 17/17, KEDA 13/13 (60/60
items), cleanup 16/16. The saved-evidence screenshot is a later rendering
of recorded output, not a new run. The
[post-release verification record](https://github.com/raiyan10/maops-kubernetes-platform/blob/0bc6de2b2e9897ce6b0f158c66e15e6b33dd83d9/docs/engineering-reviews/day-08-post-release-verification.md)
and [portfolio case study](https://github.com/raiyan10/maops-kubernetes-platform/blob/0bc6de2b2e9897ce6b0f158c66e15e6b33dd83d9/docs/portfolio-case-study.md) are
published on the project's `main`; see
[screenshots](screenshots.md#project-4-maops-kubernetes-platform).
