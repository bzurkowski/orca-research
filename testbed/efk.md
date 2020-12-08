# EFK stack

## Installation

Deploy Elasticsearch chart:

```bash
$ helm install stable/elasticsearch \
    --name elasticsearch \
    --namespace logging \
    --values $HOME/Workspace/orca/orca/helm/examples/integrations/efk/elasticsearch/orca-values.yaml
```

Deploy FluentBit chart:

```bash
$ helm install stable/fluent-bit \
    --name fluent-bit \
    --namespace logging \
    --values $HOME/Workspace/orca/orca/helm/examples/integrations/efk/fluent-bit/orca-values.yaml
```

Deploy Kibana chart:

```bash
$ helm install stable/kibana \
    --name kibana \
    --namespace logging \
    --values $HOME/Workspace/orca/orca/helm/examples/integrations/efk/kibana/orca-values.yaml
```

## Cleanup

Delete Kibana chart release:

```bash
$ helm delete --purge kibana
```

Delete FluentBit chart release:

```bash
$ helm delete --purge fluent-bit
```

Delete Elasticsearch chart release:

```bash
$ helm delete --purge elasticsearch
```
