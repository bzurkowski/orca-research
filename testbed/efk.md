# EFK stack

## Installation

Add Helm repository:

```bash
$ helm repo add elastic https://helm.elastic.co
```

Deploy Elasticsearch cluster using Helm:

```bash
$ helm install elasticsearch-master elastic/elasticsearch \
    --version 7.15.0 \
    --namespace logging \
    --create-namespace \
    --values $HOME/Workspace/orca/orca/helm/examples/integrations/efk/elasticsearch/master-values.yaml \
    --set nodeSelector.role=exp-control
```

```bash
$ helm install elasticsearch-data elastic/elasticsearch \
    --version 7.15.0 \
    --namespace logging \
    --values $HOME/Workspace/orca/orca/helm/examples/integrations/efk/elasticsearch/data-values.yaml \
    --set nodeSelector.role=exp-control
```

```bash
$ helm install elasticsearch-client elastic/elasticsearch \
    --version 7.15.0 \
    --namespace logging \
    --values $HOME/Workspace/orca/orca/helm/examples/integrations/efk/elasticsearch/client-values.yaml \
    --set nodeSelector.role=exp-control
```

Wait until Elasticsearch cluster is ready:


```bash
$ helm --namespace=logging test elasticsearch-master
```

Deploy FluentBit chart:

```bash
$ helm install filebeat elastic/filebeat \
    --version 7.15.0 \
    --namespace logging \
    --values $HOME/Workspace/orca/orca/helm/examples/integrations/efk/filebeat/values.yaml \
    --set nodeSelector.role=exp-control
```

Deploy Kibana chart:

```bash
$ helm install kibana elastic/kibana \
    --version 7.15.0 \
    --namespace logging \
    --values $HOME/Workspace/orca/orca/helm/examples/integrations/efk/kibana/values.yaml \
    --set nodeSelector.role=exp-control
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
