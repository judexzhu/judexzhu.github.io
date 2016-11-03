---
layout: post
title: "Setup Infiniband on CentOS 7"
description: "A quick note about how to config Infinibandn"
tags: [Linux, Infiniband]
---

## Just a quick note about how to config Infiniband

###install

```bash
yum -y groupinstall "Infiniband Support"
yum -y install infiniband-diags perftest gperf
```

###Uninstall

```bash
yum -y groupremove "Infiniband Support"
```

###Starting the RDMA services

```bash
systemctl start rdma
systemctl enable rdma
```

###config ib0

```bash
vi /etc/sysconfig/network-scripts/ifcfg-ib0

DEVICE=ib0
TYPE=infiniband
BOOTPROTO=static
IPADDR=192.168.4.62
NETMASK=255.255.255.0
NETWORK=192.168.4.0
BROADCAST=192.168.4.255
ONBOOT=yes
NM_CONTROLLED=no

systemctl restart network

```

