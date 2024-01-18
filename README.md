# kubevirt

## Create cluster

``` bash
kind create cluster
```

## Install kubevirt

More see [docs](https://kubevirt.io/quickstart_kind/)

``` bash
kubectl create -f https://github.com/kubevirt/kubevirt/releases/download/v1.1.1/kubevirt-operator.yaml
kubectl create -f https://github.com/kubevirt/kubevirt/releases/download/v1.1.1/kubevirt-cr.yaml
kubectl -n kubevirt patch kubevirt kubevirt --type=merge --patch '{"spec":{"configuration":{"developerConfiguration":{"useEmulation":true}}}}'
```

## Instal kwok

More see [docs](https://kwok.sigs.k8s.io/docs/user/kwok-in-cluster/)

``` bash
kubectl create -f https://github.com/wzshiming/kwok/releases/download/v0.5.0-rc.1/kwok.yaml
kubectl create -f https://github.com/wzshiming/kwok/releases/download/v0.5.0-rc.1/stage-fast.yaml
kubectl -n kube-system patch deployment kwok-controller -p '{"spec":{"template":{"spec":{"hostNetwork":true}}}}'
```

## Set up kwok for KubeVirt

``` bash
kubectl create -k ./kustomize/kwok
```

## Create Node

``` bash
kubectl create -f ./testdata/node.yaml
# NAME          STATUS   ROLES   AGE   VERSION
# kwok-node-0   Ready    agent   10s   kwok-v0.5.0
```

## Test VMI

``` bash
kubectl create -f ./testdata/vmi.yaml
```

``` bash
kubectl get vmi test-vmi
# NAME       AGE   PHASE     IP    NODENAME   READY
# test-vmi   11m   Running                    True
```
