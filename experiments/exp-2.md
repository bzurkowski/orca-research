# Experiment 2

## Preparation

## Setup OpenRCA

Deploy OpenRCA using Helm:

```bash
helm install orca $HOME/Workspace/orca/orca/helm/orca \
    --namespace rca \
    --create-namespace \
    --set image.pullPolicy=Always \
    --set image.tag=latest \
    --set ui.image.pullPolicy=Always \
    --set ui.image.tag=latest \
    --set probes.prometheus.enabled=true \
    --set probes.prometheus.url=http://prometheus-kube-prometheus-prometheus.monitoring:9090 \
    --set probes.kiali.enabled=true \
    --set probes.kiali.url=http://kiali.istio-system:20001 \
    --set probes.kiali.username=admin \
    --set probes.kiali.password=admin \
    --set ingestors.prometheus.enabled=false \
    --set ingestors.elastalert.enabled=true \
    --set ingestors.falco.enabled=true \
    --set nodeSelector.role=exp-control \
    --set ui.nodeSelector.role=exp-control \
    --set arangodb.nodeSelector.role=exp-control
```

Delete default Prometheus alert rules:

```bash
$ kubectl -n monitoring delete prometheusrules $(kubectl -n monitoring get prometheusrules |awk '{print $1}')
```

Apply custom Prometheus alert rules:

```bash
kubectl -n rca apply -f $HOME/Workspace/orca/orca/helm/examples/integrations/rules.yaml
```

### Deploy Hipster test app

Clone app repository:

```bash
$ git clone https://github.com/openrca/orca-testapps.git
$ cd ./orca-testapps
```

Create namespace:

```bash
$ kubectl create namespace hipster
$ kubectl label namespace hipster istio-injection=enabled
```

Apply Kubernetes manifests:

```bash
$ kubectl -n hipster apply -f ./hipster/kubernetes-manifests.yaml
```

Apply Istio manifests:

```bash
$ kubectl -n hipster apply -f ./hipster/istio-manifests.yaml
```

Scale Cart Service down to offload database during startup:

```bash
$ kubectl -n hipster scale deploy cartservice-v1 --replicas=0
```

### Setup application database

Deploy Percona MongoDB Operator using Helm (cluster-wide mode is not [supported](https://jira.percona.com/browse/K8SPSMDB-203)):

```bash
$ helm install --namespace hipster --create-namespace psmdb-operator percona/psmdb-operator --version 1.9.0 --set nodeSelector.role=exp-subject
```

Wait until operator is ready:

```bash
$ kubectl -n hipster get pods |grep operator
```

Deploy MongoDB cluster:

```bash
$ kubectl -n hipster apply -f ./hipster/mongodb-manifests.yaml
```

Wait until cluster is ready:

```bash
$ watch kubectl -n hipster get pods
$ watch 'kubectl -n hipster describe psmdb mdb |grep State'
```

Create DB user:

```bash
$ kubectl -n hipster exec -it $(kubectl -n hipster get pods |grep mongos |head -n1 |awk '{print $1}') -- mongo -u userAdmin -p userAdmin123456 --authenticationDatabase admin

> use hipster
> db.createUser(
  {
    user: "hipster",
    pwd: "hipster",
    roles: [
      {"role": "readWrite", "db": "hipster"}
    ]
  }
)
```

Enable sharding:

```bash
$ kubectl -n hipster exec -it $(kubectl -n hipster get pods |grep mongos |head -n1 |awk '{print $1}') -- mongo -u clusterAdmin -p clusterAdmin123456 --authenticationDatabase admin

> sh.enableSharding("hipster")
```

Create collection:

```bash
kubectl -n hipster exec -it $(kubectl -n hipster get pods |grep mongos |head -n1 |awk '{print $1}') -- mongo -u hipster -p hipster --authenticationDatabase hipster

> use hipster
> db.createCollection("carts")
```

Shard the collection:

```bash
$ kubectl -n hipster exec -it $(kubectl -n hipster get pods |grep mongos |head -n1 |awk '{print $1}') -- mongo -u clusterAdmin -p clusterAdmin123456 --authenticationDatabase admin

> sh.shardCollection("hipster.carts", {"_id": "hashed"})
```

Set MongoDB address in app environment:

```bash
$ kubectl -n hipster set env deployment/cartservice-v1 "MONGODB_URL=mongodb://hipster:hipster@mdb-mongos.hipster:27017/hipster"
```

Scale Cart Service back to the original replicas number:

```bash
$ kubectl -n hipster scale deploy cartservice-v1 --replicas=1
```
