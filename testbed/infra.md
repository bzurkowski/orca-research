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
