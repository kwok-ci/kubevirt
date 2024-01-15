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

# NAME                                          AGE   PHASE       IP    NODENAME      READY
# virtualmachineinstance.kubevirt.io/test-vmi   4s    Scheduled         kwok-node-0   False

# NAME                               READY   STATUS    RESTARTS   AGE
# pod/virt-launcher-test-vmi-8b74v   3/3     Running   0          4s
```

### KubeVirt need to implement the `status` subresource modification

``` bash
kubectl get vmi test-vmi
# NAME       AGE   PHASE       IP    NODENAME      READY
# test-vmi   10m   Scheduled         kwok-node-0   False
kubectl get vmi test-vmi --subresource=status
# Error from server (NotFound): virtualmachineinstances.kubevirt.io "test-vmi" not found
kubectl patch vmi test-vmi --subresource=status --type=merge -p '{"status":{"phase":"Running"}}'
# Error from server (NotFound): virtualmachineinstances.kubevirt.io "test-vmi" not found
```
