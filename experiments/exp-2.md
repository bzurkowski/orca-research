# Experiment 1

## Preparation

### Deploy MongoDB Cluster

Create cluster credentials:

```yaml
cat <<EOF | kubectl -npsmdb apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: mdb-secrets
type: Opaque
stringData:
  MONGODB_BACKUP_USER: backup
  MONGODB_BACKUP_PASSWORD: backup123456
  MONGODB_CLUSTER_ADMIN_USER: clusterAdmin
  MONGODB_CLUSTER_ADMIN_PASSWORD: clusterAdmin123456
  MONGODB_CLUSTER_MONITOR_USER: clusterMonitor
  MONGODB_CLUSTER_MONITOR_PASSWORD: clusterMonitor123456
  MONGODB_USER_ADMIN_USER: userAdmin
  MONGODB_USER_ADMIN_PASSWORD: userAdmin123456
  PMM_SERVER_USER: admin
  PMM_SERVER_PASSWORD: admin
EOF
```

Provision cluster from CR:

```yaml
cat <<EOF | kubectl -npsmdb apply -f -
apiVersion: psmdb.percona.com/v1-9-0
kind: PerconaServerMongoDB
metadata:
  name: mdb
  finalizers:
  - delete-psmdb-pvc
spec:
  crVersion: 1.9.0
  image: percona/percona-server-mongodb:4.4.6-8
  imagePullPolicy: Always
  allowUnsafeConfigurations: false
  updateStrategy: SmartUpdate
  upgradeOptions:
    versionServiceEndpoint: https://check.percona.com
    apply: 4.4-recommended
    schedule: "0 2 * * *"
    setFCV: false
  secrets:
    users: mdb-secrets
  pmm:
    enabled: false
    image: percona/pmm-client:2.18.0
    serverHost: psmdb-pmm-service
  replsets:
  - name: rs0
    size: 3
    storage:
      engine: wiredTiger
    affinity:
      antiAffinityTopologyKey: "kubernetes.io/hostname"
    resources:
      limits:
        cpu: "1000m"
        memory: "1G"
      requests:
        cpu: "300m"
        memory: "0.5G"
    volumeSpec:
      persistentVolumeClaim:
        resources:
          requests:
            storage: 100Mi
  sharding:
    enabled: true
    configsvrReplSet:
      size: 3
      affinity:
        antiAffinityTopologyKey: "kubernetes.io/hostname"
      resources:
        limits:
          cpu: "1000m"
          memory: "1G"
        requests:
          cpu: "300m"
          memory: "0.5G"
      volumeSpec:
        persistentVolumeClaim:
          resources:
            requests:
              storage: 100Mi
    mongos:
      size: 3
      affinity:
        antiAffinityTopologyKey: "kubernetes.io/hostname"
      resources:
        limits:
          cpu: "1000m"
          memory: "1G"
        requests:
          cpu: "300m"
          memory: "0.5G"
      expose:
        exposeType: ClusterIP
EOF
```

Wait until cluster is ready:

```bash
$ watch 'kubectl -n psmdb describe psmdb mdb |grep State'
```

Enable cluster monitoring:

```bash
$ kubectl -n psmdb edit psmdb mdb
```

```yaml
...
spec:
  ...
  pmm:
    enabled: true
    ...
```

Wait until cluster is ready:

```bash
$ watch 'kubectl -n psmdb describe psmdb mdb |grep State'
```

### Deploy Hipster test app

Create namespace:

```bash
$ kubectl create namespace hipster
$ kubectl label namespace hipster istio-injection=enabled
```

TODO

### Setup application database

Create DB user:

```bash
$ kubectl -n psmdb exec -it $(kubectl -n psmdb get pods |grep mongos |head -n1 |awk '{print $1}') -- mongo -u userAdmin -p userAdmin123456 --authenticationDatabase admin

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
$ kubectl -n psmdb exec -it $(kubectl -n psmdb get pods |grep mongos |head -n1 |awk '{print $1}') -- mongo -u clusterAdmin -p clusterAdmin123456 --authenticationDatabase admin

> sh.enableSharding("hipster")
```

Create collection:

```bash
kubectl -n psmdb exec -it $(kubectl -n psmdb get pods |grep mongos |head -n1 |awk '{print $1}') -- mongo -u hipster -p hipster --authenticationDatabase hipster

> use hipster
> db.createCollection("carts")
```

Shard the collection:

```bash
$ kubectl -n psmdb exec -it $(kubectl -n psmdb get pods |grep mongos |head -n1 |awk '{print $1}') -- mongo -u clusterAdmin -p clusterAdmin123456 --authenticationDatabase admin

> sh.shardCollection("hipster.carts", {"_id": "hashed"})
```

Set MongoDB URI in app environment:

```bash
$ kubectl -n hipster set env deployment/cartservice "MONGO_DB_URL=mongodb://hipster:hipster@mdb-mongos.psmdb:27017/hipster"
```
