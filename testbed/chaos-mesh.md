# Chaos Mesh

## Installation

Setup Helm repository:

```bash
$ helm repo add chaos-mesh https://charts.chaos-mesh.org
$ helm repo update
```

Create custom resource type:

```bash
$ curl -sSL https://mirrors.chaos-mesh.org/v0.9.1/crd.yaml | kubectl apply -f -
```

Deploy Chaos Mesh chart:

```bash
$ helm install chaos-mesh chaos-mesh/chaos-mesh \
    --namespace=chaos \
    --create-namespace \
    --set dashboard.create=true \
    --set controllerManager.nodeSelector.role=exp-control \
    --set dashboard.nodeSelector.role=exp-control
```

## Cleanup

Delete Chaos Mesh chart release:

```bash
$ helm delete --purge chaos-mesh
```
