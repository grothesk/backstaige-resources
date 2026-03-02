# backstaige-resources

GitOps repository for cluster infrastructure managed by [backstaige](https://github.com/grothesk/backstaige).

## Purpose

This repository is the single source of truth for all deployed Crossplane claims and Backstage catalog entries. Developers interact with the backstaige platform via Backstage — never directly with this repository.

```
Backstage Scaffolder
  → generates manifests + creates PR here
    → CI validates (YAML lint + Kyverno policies)
      → auto-merge on success
        → Flux CD syncs to hub cluster
          → Crossplane provisions infrastructure
```

## Structure

```
manifests/
  clusters/
    DockerCluster.{cluster-id}.yaml    # Crossplane claim
    EKSCluster.{cluster-id}.yaml       # Crossplane claim
    ProxmoxCluster.{cluster-id}.yaml   # Crossplane claim
catalog/
  resources/
    docker-cluster.{cluster-id}.yaml   # Backstage catalog entry
```

Files follow a type-first naming convention for better sorting and readability.

| Directory | Purpose |
|-----------|---------|
| `manifests/clusters/` | Crossplane claims — define the desired cluster state, synced by Flux CD |
| `catalog/resources/` | Backstage Resource entities — make clusters visible in the service catalog |

## Workflows

All operations are triggered from Backstage via Scaffolder templates:

| Operation | Template | Branch pattern |
|-----------|----------|----------------|
| Create cluster (Docker) | `docker` | `new-docker-cluster-{id}` |
| Create cluster (EKS) | `eks` | `new-cluster-request` |
| Create cluster (Proxmox) | `proxmox` | `new-proxmox-cluster-request` |
| Delete cluster (Docker) | `docker-delete` | `delete-docker-cluster-{id}` |

## CI/CD

Every pull request is validated by the `Validate PR` workflow:

1. **YAML Lint** — syntax check on all files in `manifests/`
2. **Kyverno Policy Check** — validates claims against Crossplane policies from the [backstaige](https://github.com/grothesk/backstaige) platform repo
3. **Auto-merge** — squash-merges the PR automatically if all checks pass

## Related

- [backstaige](https://github.com/grothesk/backstaige) — Platform repository (Backstage, Flux CD, Crossplane, Kyverno)

## License

[Apache License 2.0](LICENSE)
