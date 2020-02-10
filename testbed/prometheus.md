# Prometheus

## Installation

##### Deploy Helm chart

Install `stable/prometheus-operator` chart using Helm with additional flags:

  ```bash
  helm install stable/prometheus-operator \
      --name prometheus \
      --namespace monitoring \
      --values $HOME/orca/helm/examples/integrations/prometheus/orca-values.yaml
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
