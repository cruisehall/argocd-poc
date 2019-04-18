# argocd-poc

Proof of concept Argo CD project

## Contents

[CD Considerations](./docs/cd_considerations.md)

## Desired Solution Characteristics

- CD tool can be configured entirely from code (declarative configuration).
- CD tool can be deployed in a distributed fashion (e.g. one instance of the tool per Kubernetes cluster)


### Why Git Ops

- Git history doubles as audit trail (though you should still implement true auditing of Kubernetes configuration changes)
- Changing the Kubernetes cluster state from *within* the cluster is safer than granting *outside* agents permission to change cluster state.

## Why Argo CD

Of all the Git-Ops-flavored Kubernetes deployment tools I found in the [Continuous Delivery section of the CNCF Landscape](https://landscape.cncf.io/category=continuous-integration-delivery&format=card-mode&grouping=category), Argo seems to be the the most suitable for building a continuous delivery platform on top of Kubernetes.

- Declarative configuration reduces management overhead at scale.

    Unlike Spinnaker and Jenkins, Argo CD can be configured exclusively using declarative configurations (no clicking & typing required)
- Strong application definition model supports logical grouping of services.
 
    The unit-of-work is the **Application**, each of which belongs to a **Project**, optionally with other Applications.
- Free, self-hosted in Kubernetes

### Other tools evaluated

#### Weave

TODO

#### Jenkins X

TODO

#### Spinnaker

TODO

#### Go CD

- Does not seem suitable for distributed deployments (e.g. one go-cd instance per cluster), instead seems more suited for single "master" instance that deploys to **all** clusters.
- Declarative pipelines are supported, but written in XML and 

## Getting Started

### Prerequisites

1. Running Kubernetes locally (e.g. with Minikube).
1. Authenticated in `kubectl` with local cluster:

    ```sh
    $ kubectl config get-clusters
    > NAME
        <cluster-name>
    ```
1. An `argocd` namespace exists in the cluster:
    ```sh
    $ kubectl get namespaces | grep argocd
    > argocd        Active    1d
    ```
1. Argo CD is running in the `argocd` namespace:
    ```sh
    $ kubectl get deployments -n argocd
    NAME                            DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
    argocd-application-controller   1         1         1            1           18d
    argocd-repo-server              1         1         1            1           18d
    argocd-server                   1         1         1            1           18d
    dex-server                      1         1         1            1           18d
    ```


### Steps for local setup

1. Set the context to the `argocd` namespace:

    ```sh
    kubectl config set-context $(kubectl config current-context) --namespace=argocd
    ```

1. Create the Argo CD custom resources (Projects, Apps) and Project Namespaces defined in `main.yaml`

    ```sh
    kubectl apply -f main.yaml
    ```


https://kustomize.io/