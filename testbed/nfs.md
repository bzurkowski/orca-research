# NFS

### Setup server

Install NFS packages on all nodes:

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
/dev/mapper/centos-nfs    40G   33M   40G   1% /nfs
```

```bash
$ mount |grep nfs
/dev/mapper/centos-nfs on /nfs type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
...
```

Add share to `/etc/exports`:

```bash
echo "/nfs	172.17.66.0/24(rw,sync,no_root_squash)" >> /etc/exports
```

Reload exports:

```bash
$ exportfs -r
```

### Setup NFS provisioner

Install using Helm chart:

```bash
$ helm install stable/nfs-client-provisioner \
    --name nfs-provisioner \
    --namespace kube-system \
    --set nfs.server=172.17.66.31 \
    --set nfs.path=/nfs  \
    --set storageClass.name=nfs-client \
    --set storageClass.defaultClass=true \
    --set resources.requests.cpu=100m \
    --set resources.requests.memory=100Mi \
    --set resources.limits.cpu=500m \
    --set resources.limits.memory=300Mi
```
