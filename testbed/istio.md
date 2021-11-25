# Istio

## Installation

Download Istio release:

```bash
$ curl -L https://istio.io/downloadIstio | ISTIO_VERSION=1.11.4 sh -
```

Add `istioctl` binary to the system path:

```bash
$ mv ./istio-1.11.4/bin/istioctl /usr/local/bin/
```

Deploy Istio control plane:

```bash
$ istioctl install \
    -f $HOME/Workspace/orca/orca/helm/examples/integrations/istio/values.yaml \
    --set values.pilot.nodeSelector.role=exp-control \
    --set values.mixer.telemetry.nodeSelector.role=exp-control \
    --set values.kiali.nodeSelector.role=exp-control \
    --set values.gateways.istio-ingressgateway.nodeSelector.role=exp-control
```

Wait until Istio pods are up and running:

```bash
$ kubectl -n istio-system get pods
```

Deploy Kiali dashboard:

```bash
$ kubectl -n istio-system apply -f ./istio-1.11.4/samples/addons/kiali.yaml
```

Edit Prometheus address in Kiali config:

```bash
$ kubectl -n istio-system edit cm kiali
```

```yaml
  config.yaml: |
    ...
    external_services:
      custom_dashboards:
        enabled: true
      prometheus:
        url: http://prometheus-operated.monitoring:9090
```

Setup [Grafana dashboards](https://grafana.com/orgs/istio/dashboards) for Istio:

* Istio Control Plane Dashboard

    ```
    https://grafana.com/api/dashboards/7645/revisions/45/download
    ```

* Istio Mesh Dashboard

    ```
    https://grafana.com/api/dashboards/7639/revisions/45/download
    ```

* Istio Service Dashboard

    ```
    https://grafana.com/api/dashboards/7636/revisions/45/download
    ```

## Cleanup

Delete Istio deployment:

```bash
$ istioctl x uninstall --purge
```

Delete Istio namespace:

```bash
$ kubectl delete ns istio-system
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
