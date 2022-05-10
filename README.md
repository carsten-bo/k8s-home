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

## K3s

Add "cgroup_memory=1 cgroup_enable=memory" /boot/cmdline.txt

For adding a new node, see https://pet2cattle.com/2021/04/k3s-join-nodes

```
# Enabling legacy iptables on Raspbian Buster
sudo iptables -F
sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
sudo reboot
```

## Metallb

For arp to announce the ip address via wifi to the router, promisc mode needs to be set on rpi. Fort his, edit `/etc/rc.local` and add following lines

```
ifconfig wlan0 promisc
```
