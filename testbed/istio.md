# Istio

## Installation

Add Istio repository:

```bash
$ git clone https://github.com/istio/istio.git
$ git checkout -b v1.4.5 tags/1.4.5
```

Install Istio CRDs:

```bash
$ helm install install/kubernetes/helm/istio-init \
    --name istio-init \
    --namespace istio-system
```

Wait until CRDs are installed:

```bash
$ kubectl -n istio-system wait --for=condition=complete job --all
```

Install Istio:

```bash
$ helm install install/kubernetes/helm/istio \
    --name istio \
    --namespace istio-system \
    --values install/kubernetes/helm/istio/values-istio-demo.yaml
```
