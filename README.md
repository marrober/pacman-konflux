# Pacman build by Konflux

Node.js Pac-Man app with a Konflux / Pipelines-as-Code build process.

Deployment manifests live in the separate GitOps repo:
[pacman-konflux-gitops](https://github.com/marrober/pacman-konflux-gitops).

## Repository layout

| Path | Purpose |
| --- | --- |
| `src/` | Application source and container build context |
| `src/dockerfile` | Containerfile used by the Konflux docker-build pipeline |
| `.tekton/pacman-pull-request.yaml` | Pre-merge build (`pull_request` → `main`) |
| `.tekton/pacman-push.yaml` | Post-merge build (`push` → `main`) |

## Prerequisites

1. Konflux tenant namespace `pacman-konflux`
2. Konflux GitHub App installed on `marrober/pacman-konflux`
3. Application / Component applied from the GitOps repo (`konflux/`)

## How builds are triggered

| Event | PipelineRun | Image tag pattern |
| --- | --- | --- |
| Pull request to `main` | `pacman-on-pull-request` | `.../pacman:on-pr-{{revision}}` (expires in 5d) |
| Push / merge to `main` | `pacman-on-push` | `.../pacman:{{revision}}` |

Useful PR comments (for authorized users):

- `/ok-to-test` — allow a first-time contributor’s pipeline to run
- `/retest` — re-run the pull-request pipeline

## Local container build

```bash
podman build -t pacman:local -f src/dockerfile src
podman run --rm -p 8080:8080 pacman:local
```

## Related repo

- **Build:** this repository
- **Deploy / image promotion:** [pacman-konflux-gitops](https://github.com/marrober/pacman-konflux-gitops)
