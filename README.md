# argocd-poc

Proof of concept Argo CD project

## Getting Started

1. Set the context to the `argocd` namespace:

```sh
kubectl config set-context $(kubectl config current-context) --namespace=argocd
```

1. Create the Argo Project

```sh
kubectl apply -f main.yaml
```