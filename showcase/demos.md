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
