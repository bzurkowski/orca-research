# Istio

## Installation

Add Istio release repository:

```bash
$ helm repo add istio.io https://storage.googleapis.com/istio-release/releases/1.4.5/charts/
$ helm repo update
```

Install Istio CRDs:

```bash
$ helm install istio.io/istio-init --name istio-init --namespace istio-system
```

Wait until CRDs are installed:

```bash
$ kubectl -n istio-system wait --for=condition=complete job --all
```

Install Istio:

```bash
$ helm install istio.io/istio --name istio --namespace istio-system
```
