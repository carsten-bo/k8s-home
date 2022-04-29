# Home K8s Cluster

## Pre-Requisites
```
brew install fluxcd/tap/flux
```

##  Flux

```
export GITHUB_TOKEN=<your-token>
flux bootstrap github \
  --owner=carsten-bo \
  --repository=k8s-home \
  --path=cluster \
  --personal
```
create namespace
```
kubectl create namespace flux-system
```
add age key
```
cat ~/.config/sops/age/keys.txt |
    kubectl -n flux-system create secret generic sops-age \
    --from-file=age.agekey=/dev/stdin
```

install flux
```
kubectl apply --kustomize=./cluster/base/flux-system
```

## 1Password

1. Create token in 1Password https://my.1password.com/integrations/directory/
2. Create secrets
    ```
    kubectl create secret generic onepassword-token --from-literal=token="<TOKEN>" --namespace=secrets-manager

    kubectl create secret generic op-credentials --from-file=1password-credentials.json -credentials --namespace=secrets-manager
    ```

## Longhorn
Longhorn needs open-iscsi installed
```
sudo apt-get update;sudo apt-get install -y open-iscsi
```
