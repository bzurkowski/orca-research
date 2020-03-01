# Infrastructure Setup

### Provision VMs

Name: orca{i}
OS: CentOS 7

CPU:

    * Current allocation: 8
    * Maximum allocation: 8
    * Model: host-passthrough


Memory:

    * Current allocation: 40960 MiB
    * Maximum allocation: 61449 MiB

Disk:

    * Device type: VirtIO Disk
    * Storage size: 50 GiB
    * Storage format: raw
    * Cache mode: none
    * IO mode: native

NIC 1:

    * Network source: Bridge br0 Host device vnet0
    * Device model: virtio

NIC 2:

    * Network source: Bridge br1 Host device vnet1
    * Device model: virtio

### Configure network interfaces

```
$ cat /etc/sysconfig/network-scripts/ifcfg-eth0
TYPE="Ethernet"
PROXY_METHOD="none"
BROWSER_ONLY="no"
BOOTPROTO="none"
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
IPV6_ADDR_GEN_MODE="stable-privacy"
NAME="eth0"
UUID="d0fece49-7eac-4374-a471-8e51d6e3dd9c"
DEVICE="eth0"
ONBOOT="yes"
IPADDR="172.17.66.31"
PREFIX="24"
GATEWAY="172.17.66.254"
DNS1="8.8.8.8"
DNS2="172.29.128.101"
IPV6_PRIVACY="no"
```

```
$ cat /etc/sysconfig/network-scripts/ifcfg-eth0
TYPE="Ethernet"
PROXY_METHOD="none"
BROWSER_ONLY="no"
BOOTPROTO="none"
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
IPV6_ADDR_GEN_MODE="stable-privacy"
NAME="eth0"
UUID="d0fece49-7eac-4374-a471-8e51d6e3dd9c"
DEVICE="eth0"
ONBOOT="yes"
IPADDR="172.17.66.31"
PREFIX="24"
GATEWAY="172.17.66.254"
DNS1="8.8.8.8"
DNS2="172.29.128.101"
IPV6_PRIVACY="no"
```
