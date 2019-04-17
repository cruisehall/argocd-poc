# CD Considerations

## Design goals

- Argo CD makes minimal assumptions about the CD pipeline.

## Design decisions

- Argo CD deploys plain (i.e. fully-expanded) Kubernetes manifests. No Helm, Kustomize or templates.
- Each Argo CD Application in each Environment watches a Git Branch in the `manifest` Git Repo that is specific to that environment.
- Each Argo CD Application in each Environment points to a directory in the `manifest` Git Repo that is specific to that environment.

## CD Actions

These actions need to be automated to achieve continuos deployment with Argo CD according to the **Design Decisions**.

### Repo-level

| Action | Repo | Actor(s) | Comments |
|---|---|---|---|
| Deploy manifest release to environment | manifest | CD Tool | Achieved by merging a manifest release tag into the `k8s/` branch corresponding to target environment (e.g. `k8s/dev` for `dev` environment) |
| Create new manifest release | manifest | CD Tool | Entails bumping the version in the manifest repo, auto-generating the manifest YAMLs as necessary and including in the release commit. |
| Deploy new application version to environment | manifest | CD Tool | (**Create new application release**) -> **Create new manifest release** -> **Deploy manifest release to environment** |
| Create new application release | app | CD Tool | Entails creating new Docker Image w/ tag that corresponds to release tag in application git repo |
---

