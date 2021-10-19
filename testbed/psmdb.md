# Prometheus

## Installation

### Control plane

Add Percona Helm repository:

```
$ helm repo add percona https://percona.github.io/percona-helm-charts/
$ helm repo update
```

Install Percona MongoDB Operator:

```
$ helm install --namespace psmdb --create-namespace psmdb-operator percona/psmdb-operator --version 1.9.0
```

Add Percona Helm repository with PMM:

```
$ helm repo add percona2 https://percona-charts.storage.googleapis.com
$ helm repo update
```

Install PMM Server:

```
$ helm install --namespace psmdb psmdb-pmm percona2/pmm-server \
    --set platform=kubernetes \
    --version 2.21.0 \
    --set "credentials.user=admin" \
    --set "credentials.password=admin" \
    --set resource.requests.cpu=100m \
    --set resource.requests.memory=100Mi \
    --set resource.limits.cpu=4000m \
    --set resource.limits.memory=8000Mi
```

### MongoDB cluster

Create cluster credentials:

```yaml
cat <<EOF | kubectl -npsmdb apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: mdb-secrets
type: Opaque
stringData:
  MONGODB_BACKUP_USER: admin
  MONGODB_BACKUP_PASSWORD: admin
  MONGODB_CLUSTER_ADMIN_USER: admin
  MONGODB_CLUSTER_ADMIN_PASSWORD: admin
  MONGODB_CLUSTER_MONITOR_USER: admin
  MONGODB_CLUSTER_MONITOR_PASSWORD: admin
  MONGODB_USER_ADMIN_USER: admin
  MONGODB_USER_ADMIN_PASSWORD: admin
  PMM_SERVER_USER: admin
  PMM_SERVER_PASSWORD: admin
EOF
```

Provision cluster from CR:

```yaml
cat <<EOF | kubectl -npsmdb apply -f -
apiVersion: psmdb.percona.com/v1-10-0
kind: PerconaServerMongoDB
metadata:
  name: mdb
  finalizers:
  - delete-psmdb-pvc
spec:
  platform: kubernetes
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
    enabled: true
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
        cpu: "300m"
        memory: "0.5G"
      requests:
        cpu: "300m"
        memory: "0.5G"
    volumeSpec:
      persistentVolumeClaim:
        resources:
          requests:
            storage: 100Mi
  - name: rs1
    size: 3
    storage:
      engine: wiredTiger
    affinity:
      antiAffinityTopologyKey: "kubernetes.io/hostname"
    resources:
      limits:
        cpu: "300m"
        memory: "0.5G"
      requests:
        cpu: "300m"
        memory: "0.5G"
    volumeSpec:
      persistentVolumeClaim:
        resources:
          requests:
            storage: 100Mi
  - name: rs2
    size: 3
    storage:
      engine: wiredTiger
    affinity:
      antiAffinityTopologyKey: "kubernetes.io/hostname"
    resources:
      limits:
        cpu: "300m"
        memory: "0.5G"
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
          cpu: "300m"
          memory: "0.5G"
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
          cpu: "300m"
          memory: "0.5G"
        requests:
          cpu: "300m"
          memory: "0.5G"
      expose:
        exposeType: ClusterIP
EOF
```

## Cleanup

Delete PSMDB chart releases:

```bash
$ helm -n psmdb delete psmdb-pmm
$ helm -n psmdb delete psmdb-operator
```

Remove leftover Prometheus CRDs:

```bash
kubectl delete crds $(kubectl get crds |grep -i percona |awk '{print $1}')
```
