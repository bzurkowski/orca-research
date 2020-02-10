# EFK stack

## Installation

##### Deploy Helm charts

Deploy Elasticsearch:

  ```bash
  $ helm install stable/elasticsearch \
      --name elasticsearch \
      --namespace logging \
      --values $HOME/orca/helm/examples/integrations/efk/elasticsearch/orca-values.yaml
  ```

Deploy FluentBit:

  ```bash
  $ helm install stable/fluent-bit \
      --name fluent-bit \
      --namespace logging \
      --values $HOME/orca/helm/examples/integrations/efk/fluent-bit/orca-values.yaml
  ```

Deploy Kibana:

  ```bash
  $ helm install stable/kibana \
      --name kibana \
      --namespace logging \
      --values $HOME/orca/helm/examples/integrations/efk/kibana/orca-values.yaml
  ```
