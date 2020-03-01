# Kubernetes

### Setup Kubespray

Clone Kubespray repository:

```bash
$ git clone https://github.com/kubernetes-sigs/kubespray.git
$ cd ./kubespray
```

Checkout:

```bash
$ git checkout -b v2.12.2 tags/v2.12.2
```

Create and activate a virtual env:

```bash
$ virtualenv -p $(which python3) ~/.venvs/kubespray
$ source ~/.venvs/kubespray/bin/activate
```

Install dependencies:

```bash
$ pip install -r requirements.txt
```

### Setup target hosts

Copy SSH keys:

```bash
$ ssh-copy-id root@172.17.66.31
$ ssh-copy-id root@172.17.66.32
$ ssh-copy-id root@172.17.66.33
$ ssh-copy-id root@172.17.66.34
```

Enable IP forwarding:

```bash
$ echo "net.ipv4.ip_forward = 1" >> /etc/sysctl.conf
$ sysctl -p
```

Disable firewall:

```bash
$ systemctl stop firewalld
$ systemctl disable firewalld
```

### Prepare inventory

Bootstrap base inventory:

```bash
$ cp -r inventory/sample inventory/orca
```

Declare target host IPs:

```bash
$ declare -a IPS=(172.17.66.31 172.17.66.32 172.17.66.33 172.17.66.34)
```

Populate inventory:

```bash
$ CONFIG_FILE=inventory/orca/hosts.yml python3 contrib/inventory_builder/inventory.py ${IPS[@]}
```

Update `inventory/orca/hosts.yaml`:

```yaml
all:
  hosts:
    node1:
      ansible_host: 172.17.66.31
      ip: 10.10.10.31
      ansible_user: root
    node2:
      ansible_host: 172.17.66.32
      ip: 10.10.10.32
      ansible_user: root
    node3:
      ansible_host: 172.17.66.33
      ip: 10.10.10.33
      ansible_user: root
    node4:
      ansible_host: 172.17.66.34
      ip: 10.10.10.34
      ansible_user: root
  children:
    kube-master:
      hosts:
        node1:
    kube-node:
      hosts:
        node1:
        node2:
        node3:
        node4:
    etcd:
      hosts:
        node1:
        node2:
        node3:
    k8s-cluster:
      children:
        kube-master:
        kube-node:
    calico-rr:
      hosts: {}
```

Update `inventory/orca/group_vars/k8s-cluster/k8s-cluster.yaml`

```yaml
kube_version: v1.15.3
```

Update `inventory/orca/group_vars/k8s-cluster/addons.yml`:

```yaml
helm_enabled: true
registry_enabled: true
metrics_server_enabled: true
```

### Deploy cluster

Run Ansible playbook:

```bash
$ ansible-playbook -i ./inventory/orca/hosts.yml cluster.yml -b -v
```

