# `flux-maintanance-windows-sample`

## Getting started

### Pre-requisits

- Running cluster (AKS, K3s etc)
- Installed Flux CLI

### Set up

Fork repository

```sh
git fork https://github.com/MahrRah/flux-maintanance-windows-sample.git
```

#### Create `GitRepository` resource on cluster

```sh
flux create source git config-poc \
    --url="https://github.com/<your-github-hande>/flux-maintanance-windows-sample.git" \
    --username=<user-name> \
    --password=<PAT-token> \
    --branch=main \
    --interval=1m \
    --git-implementation=libgit2 \
    --silent
```

Two samples:

1. `edge-01`: Updates only possible to cluster during a maintenance window
2. `edge-02`: Applications have configurations that needs to be updated live as well as during maintenance windows.

#### Create `kustomization` resources

```sh
flux create kustomization infrastructure-poc \
  --path="./clusters/pump1/infra" \
  --source=config-poc \
  --prune=true \
  --interval=1m
```

```sh
flux create kustomization apps-poc \
  --depends-on=infrastructure-poc \
  --path="./clusters/pump1/sets/production/" \
  --source=config-poc \
  --prune=true \
  --interval=1m
```

# Use Maintenance windows
