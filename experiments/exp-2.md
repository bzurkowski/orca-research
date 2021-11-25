# Experiment 2

## Preparation

## Install application platform

Install platform components:

- [Monitoring (Promehteus)](prometheus.md)
- [Logging (Elasticsearch, Filebeat, Kibana)](efk.md)
- [Log alerting (Elastalert)](elastlaert.md)
- [Service mesh (Istio)](istio.md)
- [Chaos testing (Chaos Mesh)](chaos-mesh.md)
- [Root Cause Analysis](orca.md)

## Configure OpenRCA

Delete default Prometheus alert rules:

```bash
$ kubectl -n monitoring delete prometheusrules $(kubectl -n monitoring get prometheusrules |awk '{print $1}')
```

Apply custom Prometheus alert rules:

```bash
kubectl -n rca apply -f $HOME/Workspace/orca/orca/helm/examples/integrations/rules.yaml
```

### Deploy Hipster test app

Create namespace:

```bash
$ kubectl create namespace hipster
$ kubectl label namespace hipster istio-injection=enabled
```

Apply Kubernetes manifests:

```bash
$ kubectl -n hipster apply -f $HOME/Workspace/orca/orca-testapps/hipster/kubernetes-manifests.yaml
```

Apply Istio manifests:

```bash
$ kubectl -n hipster apply -f $HOME/Workspace/orca/orca-testapps/hipster/istio-manifests.yaml
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
$ kubectl -n hipster apply -f $HOME/Workspace/orca/orca-testapps/hipster/mongodb-manifests.yaml
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

## Cleanup

Delete Hipster test app:

```bash
$ kubectl -n hipster delete -f $HOME/Workspace/orca/orca-testapps/hipster/kubernetes-manifests.yaml
$ kubectl -n hipster delete -f $HOME/Workspace/orca/orca-testapps/hipster/istio-manifests.yaml
```

Delete MongoDB cluster:

```bash
$ kubectl -n hipster delete -f $HOME/Workspace/orca/orca-testapps/hipster/mongodb-manifests.yaml
```

Wait until MongoDB pods are terminated:

```bash
$ watch kubectl -n hipster get pod
```

Delete MongoDB Operator:

```bash
$ helm -n hipster delete psmdb-operator
```

Delete test app namespace:

```bash
$ kubectl delete ns hipster
```
