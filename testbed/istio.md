# Istio

## Installation

Download Istio release:

```bash
$ curl -L https://istio.io/downloadIstio | ISTIO_VERSION=1.6.14 sh -
```

Add `istioctl` binary to the system path:

```bash
$ mv istio-1.6.14/bin/istioctl /usr/local/bin/
```

Deploy Istio control plane:

```bash
$ istioctl install \
    -f $HOME/Workspace/orca/orca/helm/examples/integrations/istio/orca-values.yaml \
    --set values.kiali.prometheusAddr=http://prometheus-kube-prometheus-prometheus.monitoring:9090 \
    --set values.pilot.nodeSelector."kubernetes\.io/hostname"=node2 \
    --set values.mixer.telemetry.nodeSelector."kubernetes\.io/hostname"=node2 \
    --set values.kiali.nodeSelector."kubernetes\.io/hostname"=node2 \
    --set values.gateways.istio-ingressgateway.nodeSelector."kubernetes\.io/hostname"=node2
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
$ istioctl manifest generate -f $HOME/Workspace/orca/orca/helm/examples/integrations/istio/orca-values.yaml | kubectl delete -f -
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
