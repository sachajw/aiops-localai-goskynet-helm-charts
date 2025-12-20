# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Helm chart repository for deploying **LocalAI** (go-skynet project) to Kubernetes. LocalAI is an open-source local AI inference server. The main chart is located at `charts/local-ai/`.

## Common Commands

```bash
# Lint the chart (validate before committing)
helm lint charts/local-ai

# Template the chart (test rendering with custom values)
helm template local-ai charts/local-ai -f values.yaml

# Install locally for testing
helm install local-ai charts/local-ai -f values.yaml

# Upgrade an existing release
helm upgrade local-ai charts/local-ai -f values.yaml

# Dry-run install to see what would be deployed
helm install local-ai charts/local-ai -f values.yaml --dry-run
```

## Chart Structure

```
charts/local-ai/
├── Chart.yaml              # Chart metadata (version, appVersion)
├── values.yaml             # Default configuration
├── values-pi3s.yaml        # Alternative config for Raspberry Pi 3s
└── templates/
    ├── _helpers.tpl        # Template helper functions (naming, labels)
    ├── deployment.yaml     # Main LocalAI deployment
    ├── service.yaml        # ClusterIP/NodePort service
    ├── ingress.yaml        # Optional ingress resource
    ├── pvcs.yaml           # Persistent volume claims
    ├── _pvc.yaml           # PVC helper template
    ├── configmap-prompt-templates.yaml  # AI prompt templates
    └── configmap-models-configs.yaml    # Model configuration
```

## Key Architecture Points

- **Deployment** supports init containers for copying prompt templates and model configs from ConfigMaps
- **Storage** options: PVC, hostPath, or ephemeral (mutually exclusive; PVC takes precedence)
- **GPU support** via `runtimeClassName` configuration
- **ConfigMap checksums** in deployment annotations trigger pod restarts on config changes

## Release Process

Releases are automated via GitHub Actions (`.github/workflows/release.yml`):
- Pushes to `main` trigger the chart-releaser-action
- Chart version changes in `Chart.yaml` create new GitHub releases
- Charts are published to `https://go-skynet.github.io/helm-charts/`

## When Modifying

- Bump `version` in `Chart.yaml` for any chart changes
- Bump `appVersion` when updating the LocalAI container version
- Update `README.md` if adding new values or changing configuration options
