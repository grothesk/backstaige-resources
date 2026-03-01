# backstaige-resources

GitOps-Repository for cluster infrastructure managed by [backstaige](https://github.com/grothesk/backstaige).

## Purpose

This repository is the single source of truth for all deployed Crossplane claims and Backstage catalog entries. Developers interact with the backstaige platform via Backstage — never directly with this repository.

```
Backstage Scaffolder
  → generates manifests + creates PR here
    → CI validates (YAML lint + Kyverno policies)
      → auto-merge on success
        → Argo CD syncs to hub cluster
          → Crossplane provisions infrastructure
```

## Structure

```
clusters/
  {cluster-id}.DockerCluster.yaml   # Crossplane claim
  {cluster-id}.resource.yaml        # Backstage catalog entry
```

Each cluster produces two files:

| File | Purpose |
|------|---------|
| `*.DockerCluster.yaml` | Crossplane claim — defines the desired cluster state |
| `*.resource.yaml` | Backstage Resource entity — makes the cluster visible in the catalog |

## Workflows

All operations are triggered from Backstage via Scaffolder templates:

| Operation | Template | Branch pattern |
|-----------|----------|----------------|
| Create cluster | `docker` | `new-docker-cluster-{id}` |
| Upgrade K8s version | `docker-upgrade` | `upgrade-docker-cluster-{id}` |
| Delete cluster | `docker-delete` | `delete-docker-cluster-{id}` |

## CI/CD

Every pull request is validated by the `Validate PR` workflow:

1. **YAML Lint** — syntax check on all files in `clusters/`
2. **Kyverno Policy Check** — validates claims against Crossplane policies from the [backstaige](https://github.com/grothesk/backstaige) platform repo
3. **Auto-merge** — squash-merges the PR automatically if all checks pass

## Related

- [backstaige](https://github.com/grothesk/backstaige) — Platform repository (Backstage, Argo CD, Crossplane, Kyverno)

## License

[Apache License 2.0](LICENSE)
