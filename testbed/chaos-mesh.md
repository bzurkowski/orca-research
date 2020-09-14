# Chaos Mesh

## Installation

Setup Helm repository:

```bash
$ helm repo add chaos-mesh https://charts.chaos-mesh.org
```

Create custom resource type:

```bash
$ curl -sSL https://mirrors.chaos-mesh.org/v0.9.1/crd.yaml | kubectl apply -f -
```

Install `chaos-mesh/chaos-mesh` chart using Helm:

```bash
$ helm install chaos-mesh/chaos-mesh \
    --name=chaos-mesh \
    --namespace=chaos-testing \
    --set dashboard.create=true
```

## Cleanup

```bash
$ helm delete --purge chaos-mesh
```
