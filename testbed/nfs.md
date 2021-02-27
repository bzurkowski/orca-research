# NFS

### Setup server

Install NFS packages on **all nodes**:

```bash
$ yum install -y nfs-utils
```

Start NFS service on master node:

```bash
$ systemctl start nfs-server
$ systemctl enable nfs-server
```

### Define shares

Having `nfs` mount point already prepared:

```bash
$ df -h |grep nfs
/dev/vda2       296G   65M  281G   1% /nfs
```

```bash
$ mount |grep nfs
/dev/vda2 on /nfs type ext4 (rw,relatime,seclabel,data=ordered)
...
```

Add share to `/etc/exports`:

```bash
echo "/nfs	172.17.80.0/24(rw,sync,no_root_squash)" >> /etc/exports
```

Reload exports:

```bash
$ exportfs -r
```

### Setup NFS provisioner

Install using Helm chart:

```bash
$ helm install nfs-provisioner stable/nfs-client-provisioner \
    --namespace nfs \
    --create-namespace \
    --set nfs.server=172.17.80.143 \
    --set nfs.path=/nfs  \
    --set storageClass.name=nfs \
    --set storageClass.defaultClass=true \
    --set resources.requests.cpu=100m \
    --set resources.requests.memory=100Mi \
    --set resources.limits.cpu=500m \
    --set resources.limits.memory=300Mi \
    --set nodeSelector.role=exp-control
```
