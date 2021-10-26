# EFK stack

## Installation

Deploy Elasticsearch chart:

```bash
$ helm install elasticsearch stable/elasticsearch \
    --namespace logging \
    --create-namespace \
    --values $HOME/Workspace/orca/orca/helm/examples/integrations/efk/elasticsearch/orca-values.yaml
```

Wait until Elasticsearch cluster is ready:


```bash
$ watch 'kubectl -n logging exec -it elasticsearch-master-0 -- curl -v localhost:9200/_cat/health |grep green'
1635264067 16:01:07 elasticsearch green 6 2 0 0 0 0 0 0 - 100.0%
```

Deploy FluentBit chart:

```bash
$ helm install fluent-bit stable/fluent-bit \
    --namespace logging \
    --values $HOME/Workspace/orca/orca/helm/examples/integrations/efk/fluent-bit/orca-values.yaml
```

Deploy Kibana chart:

```bash
$ helm install kibana stable/kibana \
    --namespace logging \
    --values $HOME/Workspace/orca/orca/helm/examples/integrations/efk/kibana/orca-values.yaml
```

## Cleanup

Delete Kibana chart release:

```bash
$ helm -n logging delete kibana
```

Delete FluentBit chart release:

```bash
$ helm -n logging delete fluent-bit
```

Delete Elasticsearch chart release:

```bash
$ helm -n logging delete elasticsearch
```
