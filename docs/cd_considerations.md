# CD Considerations

## Design goals

- Argo CD makes minimal assumptions about the CD pipeline.
- Argo CD makes minimal assumptions about the Manifest repo.

## Design decisions

- Argo CD deploys plain (i.e. fully-expanded) Kubernetes manifests. 
  - No Helm, Kustomize or templates will be referenced by Argo CD, as this would violate the goal of minimizing the # of assumptions about the upstream CD pipeline.
- A namespace is created for each `Application`.
- Each Argo CD `Application` in each Environment watches a Git Branch in the `manifest` Git Repo that is specific to that environment.

    ```yaml
    apiVersion: argoproj.io/v1alpha1
    kind: Application
    spec:
      syncPolicy:
        targetRevision: k8s/dev
    ```

- Each Argo CD Application in each Environment points to a directory in the `manifest` Git Repo that is specific to that environment.

    ```yaml
    apiVersion: argoproj.io/v1alpha1
    kind: Application
    spec:
      syncPolicy:
        path: k8s/dev
    ```

    NOTE: Making the **path** and the **targetRevision** equal to the same string can simplify the auto-generation of Argo CD Application definitions, but this is not a requirement.

- Directory Structure:
  
  Assuming there are 3 environments (dev, stage & prod), and the app requires a `deployment.yaml` and `service.yaml` manifests:

  ```sh
  k8s.d/                      # pre-compiled kustomize templates
    kustomize.yaml
    base/                     # base kustomize target
      deployment.yaml
      service.yaml
      kustomize.yaml |
        resources:
        - deployment.yaml
        - service.yaml
    dev/
      [deployment.yaml]       # optional overlay, same for stage & prod
      [service.yaml]          # optional overlay
      kustomize.yaml |
        bases:
        - ../base
      #  patches:             # if applicable
      #  - deployment.yaml
      #  - service.yaml
    stage/
      kustomize.yaml
    prod/
      kustomize.yaml
  k8s/                        # compiled manifests, auto-generated from kustomize templates
    dev/
      main.yaml
    stage/
      main.yaml
    prod/
      main.yaml
  ```

## Open Questions

- Cardinality of Docker Image Registries
  - One per cluster?
  - One per account/project?
  - Shared across all clusters, accounst/projects?

## CD Actions

These actions need to be automated to achieve continuos deployment with Argo CD according to the **Design Decisions**.

### Repo-level

| Action | Repo | Actor(s) | Comments |
|---|---|---|---|
| Create new manifest release | manifest | CD Tool | Entails bumping the version in the manifest repo, auto-generating the manifest YAMLs and including in the release commit. |
| Deploy manifest release to environment | manifest | CD Tool | Achieved by merging a manifest release tag into the `k8s/` branch corresponding to target environment (e.g. `k8s/dev` for `dev` environment) |
| Create new application release | app | CD Tool | Entails creating new Docker Image w/ tag that corresponds to release tag in application git repo, push to Registry that is accessible by appropriate environments |
| Deploy new application version to environment | manifest | CD Tool | (**Create new application release**) -> **Create new manifest release** referencing the newly-created Application Version -> **Deploy manifest release to environment** |
---