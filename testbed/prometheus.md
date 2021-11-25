# Prometheus

## Installation

Setup Helm repository:

```bash
$ helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
$ helm repo update
```

Deploy Prometheus chart:

```bash
$ helm install prometheus prometheus-community/kube-prometheus-stack \
    --version 12.7.0 \
    --namespace monitoring \
    --create-namespace \
    --values $HOME/Workspace/orca/orca/helm/examples/integrations/prometheus/values.yaml \
    --set prometheusOperator.nodeSelector.role=exp-control \
    --set prometheus.prometheusSpec.scrapeInterval=15s \
    --set prometheus.prometheusSpec.nodeSelector.role=exp-control \
    --set alertmanager.alertmanagerSpec.nodeSelector.role=exp-control \
    --set grafana.nodeSelector.role=exp-control \
    --set kube-state-metrics.nodeSelector.role=exp-control
```

```yaml
# prometheus-operator.yaml
...
config:
  global:
    resolve_timeout: 5m
  receivers:
  - name: webhook
    webhook_configs:
    - url: http://orca.rca:5000/v1/ingestor/prometheus
  route:
    group_by: ['job']
    group_interval: 30s
    group_wait: 30s
    receiver: 'webhook'
    repeat_interval: 12h
    routes: []
```

## Cleanup

Delete Prometheus chart release:

```bash
$ helm -n monitoring delete prometheus
```

Remove Prometheus CRDs:

```bash
$ kubectl delete crds $(kubectl get crds |grep "monitoring.coreos.com" |awk '{print $1}')
```

Delete monitoring namespace:

```bash
$ kubectl delete ns monitoring
```
