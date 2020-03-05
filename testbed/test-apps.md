# Test Applications

## Hipster Shop

Clone app repository:

```bash
$ git clone https://github.com/GoogleCloudPlatform/microservices-demo.git
```

Create namespace:

```bash
$ kubectl create namespace hipster-shop
$ kubectl label namespace hipster-shop istio-injection=enabled
```

Apply Kubernetes manifests:

```bash
$ kubectl -n hipster-shop apply -f ./release/kubernetes-manifests.yaml
```

Apply Istio manifests:

```bash
$ kubectl -n hipster-shop apply -f ./release/istio-manifests.yaml
```
