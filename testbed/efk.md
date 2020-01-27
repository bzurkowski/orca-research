# EFK stack

## Installation

##### Deploy Helm chart

  ```bash
  helm install stable/elastic-stack \
      --name efk \
      --namespace logging \
      --set elasticsearch.master.persistence.enabled=false \
      --set elasticsearch.data.persistence.enabled=false \
      --set elasticsearch.master.resources.requests.cpu=100m \
      --set elasticsearch.master.resources.requests.memory=100Mi \
      --set elasticsearch.master.resources.limits.cpu=4000m \
      --set elasticsearch.master.resources.limits.memory=4000Mi \
      --set elasticsearch.data.resources.requests.cpu=100m \
      --set elasticsearch.data.resources.requests.memory=100Mi \
      --set elasticsearch.data.resources.limits.cpu=4000m \
      --set elasticsearch.data.resources.limits.memory=4000Mi \
      --set elasticsearch.client.resources.requests.cpu=100m \
      --set elasticsearch.client.resources.requests.memory=100Mi \
      --set elasticsearch.client.resources.limits.cpu=4000m \
      --set elasticsearch.client.resources.limits.memory=4000Mi \
      --set logstash.enabled=false \
      --set fluent-bit.enabled=true \
      --set fluent-bit.resources.requests.cpu=100m \
      --set fluent-bit.resources.requests.memory=100Mi \
      --set fluent-bit.resources.limits.cpu=4000m \
      --set fluent-bit.resources.limits.memory=4000Mi \
      --set fluent-bit.backend.type=es \
      --set fluent-bit.backend.es.host=efk-elasticsearch-client.logging \
      --set kibana.enabled=true \
      --set kibana.env.ELASTICSEARCH_HOSTS=http://efk-elasticsearch-client.logging:9200 \
      --set elasticsearch-curator.enabled=true
  ```
