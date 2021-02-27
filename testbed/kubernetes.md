# Kubernetes

### Setup Kubespray

Clone Kubespray repository:

```bash
$ git clone https://github.com/kubernetes-sigs/kubespray.git
$ cd ./kubespray
```

Checkout:

```bash
$ git checkout -b v2.12.10 tags/v2.12.10
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
$ ssh-copy-id root@172.17.80.128
$ ssh-copy-id root@172.17.80.129
$ ssh-copy-id root@172.17.80.130
$ ssh-copy-id root@172.17.80.131
...
$ ssh-copy-id root@172.17.80.143
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

Install NTP:

```bash
$ yum install -y ntp
$ systemctl start ntpd
$ systemctl enable ntpd
```

### Prepare inventory

Bootstrap base inventory:

```bash
$ cp -r inventory/sample inventory/orca
```

Declare target host IPs:

```bash
$ python3 -c 'print("declare -a IPS=(%s)" % " ".join(["172.17.80.%s" % i for i in range(128, 144)]))'
```

```bash
$ declare -a IPS=(172.17.80.128 172.17.80.129 172.17.80.130 172.17.80.131 172.17.80.132 172.17.80.133 172.17.80.134 172.17.80.135 172.17.80.136 172.17.80.137 172.17.80.138 172.17.80.139 172.17.80.140 172.17.80.141 172.17.80.142 172.17.80.143)
```

Populate inventory:

```bash
$ CONFIG_FILE=inventory/orca/hosts.yml python3 contrib/inventory_builder/inventory.py ${IPS[@]}
```

Update `inventory/orca/hosts.yml`:

```yaml
all:
  hosts:
    node1:
      ansible_host: 172.17.80.128
      ip: 10.10.10.128
      ansible_user: root
    node2:
      ansible_host: 172.17.80.129
      ip: 10.10.10.129
      ansible_user: root
    node3:
      ansible_host: 172.17.80.130
      ip: 10.10.10.130
      ansible_user: root
    node4:
      ansible_host: 172.17.80.131
      ip: 10.10.10.131
      ansible_user: root
    node5:
      ansible_host: 172.17.80.132
      ip: 10.10.10.132
      ansible_user: root
    node6:
      ansible_host: 172.17.80.133
      ip: 10.10.10.133
      ansible_user: root
    node7:
      ansible_host: 172.17.80.134
      ip: 10.10.10.134
      ansible_user: root
    node8:
      ansible_host: 172.17.80.135
      ip: 10.10.10.135
      ansible_user: root
    node9:
      ansible_host: 172.17.80.136
      ip: 10.10.10.136
      ansible_user: root
    node10:
      ansible_host: 172.17.80.137
      ip: 10.10.10.137
      ansible_user: root
    node11:
      ansible_host: 172.17.80.138
      ip: 10.10.10.138
      ansible_user: root
    node12:
      ansible_host: 172.17.80.139
      ip: 10.10.10.139
      ansible_user: root
    node13:
      ansible_host: 172.17.80.140
      ip: 10.10.10.140
      ansible_user: root
    node14:
      ansible_host: 172.17.80.141
      ip: 10.10.10.141
      ansible_user: root
    node15:
      ansible_host: 172.17.80.142
      ip: 10.10.10.142
      ansible_user: root
    node16:
      ansible_host: 172.17.80.143
      ip: 10.10.10.143
      ansible_user: root
  children:
    kube-master:
      hosts:
        node1:
        node2:
    kube-node:
      hosts:
        node1:
        node2:
        node3:
        node4:
        node5:
        node6:
        node7:
        node8:
        node9:
        node10:
        node11:
        node12:
        node13:
        node14:
        node15:
        node16:
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

Update `inventory/orca/group_vars/k8s-cluster/k8s-cluster.yml`:

```yaml
kube_version: v1.16.7
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

## Upgrade

In order to upgrade the cluster run:

```bash
$ ansible-playbook upgrade-cluster.yml -b -i ./inventory/orca/hosts.yml -e kube_version=v1.16.7
```
