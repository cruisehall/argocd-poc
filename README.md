# argocd-poc

Proof of concept Argo CD project

## Contents

[CD Considerations](./docs/cd_considerations.md)

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