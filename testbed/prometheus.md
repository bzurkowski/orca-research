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
    --values $HOME/Workspace/orca/orca/helm/examples/integrations/prometheus/orca-values.yaml \
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
$ helm -n monitoring uninstall prometheus
```

Remove leftover Prometheus CRDs:

```bash
kubectl delete crd alertmanagerconfigs.monitoring.coreos.com
kubectl delete crd alertmanagers.monitoring.coreos.com
kubectl delete crd podmonitors.monitoring.coreos.com
kubectl delete crd probes.monitoring.coreos.com
kubectl delete crd prometheuses.monitoring.coreos.com
kubectl delete crd prometheusrules.monitoring.coreos.com
kubectl delete crd servicemonitors.monitoring.coreos.com
kubectl delete crd thanosrulers.monitoring.coreos.com
```
