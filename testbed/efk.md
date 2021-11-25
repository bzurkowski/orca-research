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

Wait until all Elasticsearch pods are ready:

```bash
$ watch kubectl -n logging get pods
```

Wait until Elasticsearch cluster is ready:

```bash
$ helm --namespace=logging test elasticsearch-master
```

Deploy Filebeat chart:

```bash
$ helm install filebeat elastic/filebeat \
    --version 7.15.0 \
    --namespace logging \
    --values $HOME/Workspace/orca/orca/helm/examples/integrations/efk/filebeat/values.yaml
```

Deploy Kibana chart:

```bash
$ helm install kibana elastic/kibana \
    --version 7.15.0 \
    --namespace logging \
    --values $HOME/Workspace/orca/orca/helm/examples/integrations/efk/kibana/values.yaml \
    --set nodeSelector.role=exp-control
```

Wait until buffered logs are pushed to Elasticsearch (10-15 minutes)...

Port-forward Kibana dashboard:

```bash
$ kubectl -n logging port-forward svc/kibana 4444:5601
```

Open [Kibana dashboard](http://localhost:4444) and create index pattern:

![](/assets/images/efk-index-pattern.png)

## Cleanup

Delete Kibana chart release:

```bash
$ helm -n logging delete kibana
```

Delete Filebeat chart release:

```bash
$ helm -n logging delete filebeat
```

Delete Elasticsearch chart release:

```bash
$ helm -n logging delete elasticsearch-client
$ helm -n logging delete elasticsearch-master
$ helm -n logging delete elasticsearch-data
```

Delete logging namespace:

```bash
$ kubectl delete ns logging
```
