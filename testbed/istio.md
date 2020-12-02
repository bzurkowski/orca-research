# Istio

## Installation

Download Istio release:

```bash
$ curl -L https://istio.io/downloadIstio | ISTIO_VERSION=1.8.0 sh -
```

Add `istioctl` binary to the system path:

```bash
$ mv istio-1.8.0/bin/istioctl /usr/local/bin/
```

Apply manifest for Istio Operator:

```bash
$ istioctl manifest apply \
    -f $HOME/orca/helm/examples/integrations/istio/orca-values.yaml \
    --set values.kiali.prometheusAddr=http://prometheus-kube-prometheus-prometheus.monitoring:9090
```

## Cleanup

Delete Istio deployment:

```bash
$ istioctl manifest generate -f $HOME/orca/helm/examples/integrations/istio/orca-values.yaml | kubectl delete -f -
```

## Helm-based installation (deprecated)

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
