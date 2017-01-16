---
layout: post
title: "Install Kubernetes 1.5.1 HA with kubeadm"
description: "Install Kubernetes 1.5.1 HA with kubeadm"
tags: [Kubernetes, Docker]
---

# ENV

## Node   IP

| Node           | IP           |
|:--------       |:-------:     |
| s7kuberma01    | 10.1.51.31   |
| s7kuberma02    | 10.1.51.32   |
| s7kuberma03    | 10.1.51.33   |
| s7kubersla01   | 10.1.51.34   |
| s7kubersla02   | 10.1.51.35   |

### vi /etc/hosts

10.1.51.31  s7kuberma01

10.1.51.32  s7kuberma02

10.1.51.33  s7kuberma03

10.1.51.34  s7kubersla01

10.1.51.35  s7kubersla02

# architecture looks like this 
<figure>
	<a href="/images/kubernetes-HA.png"><img src="/images/kubernetes-HA.png" alt=""></a>
</figure>

# Master Nodes

## install etcd cluster in the 3 master nodes

```bash
yum install -y etcd
```

## Configuration

```bash
vi /etc/etcd/etcd.conf
```

#### s7kuberma01 etcd 

```ruby
# [member]
ETCD_NAME=s7kuberma01
ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
#ETCD_WAL_DIR=""
#ETCD_SNAPSHOT_COUNT="10000"
#ETCD_HEARTBEAT_INTERVAL="100"
#ETCD_ELECTION_TIMEOUT="1000"
ETCD_LISTEN_PEER_URLS="http://10.1.51.31:2380"
ETCD_LISTEN_CLIENT_URLS="http://10.1.51.31:2379,http://localhost:2379"
#ETCD_MAX_SNAPSHOTS="5"
#ETCD_MAX_WALS="5"
#ETCD_CORS=""
#
#[cluster]
ETCD_INITIAL_ADVERTISE_PEER_URLS="http://10.1.51.31:2380"
# if you use different ETCD_NAME (e.g. test), set ETCD_INITIAL_CLUSTER value for this name, i.e. "test=http://..."
ETCD_INITIAL_CLUSTER="s7kuberma01=http://10.1.51.31:2380,s7kuberma02=http://10.1.51.32:2380,s7kuberma03=http://10.1.51.33:2380"
ETCD_INITIAL_CLUSTER_STATE="new"
ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster"
ETCD_ADVERTISE_CLIENT_URLS="http://10.1.51.31:2379"
```
#### s7kuberma02 etcd 

```ruby
# [member]
ETCD_NAME=s7kuberma02
ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
#ETCD_WAL_DIR=""
#ETCD_SNAPSHOT_COUNT="10000"
#ETCD_HEARTBEAT_INTERVAL="100"
#ETCD_ELECTION_TIMEOUT="1000"
ETCD_LISTEN_PEER_URLS="http://10.1.51.32:2380"
ETCD_LISTEN_CLIENT_URLS="http://10.1.51.32:2379,http://localhost:2379"
#ETCD_MAX_SNAPSHOTS="5"
#ETCD_MAX_WALS="5"
#ETCD_CORS=""
#
#[cluster]
ETCD_INITIAL_ADVERTISE_PEER_URLS="http://10.1.51.32:2380"
# if you use different ETCD_NAME (e.g. test), set ETCD_INITIAL_CLUSTER value for this name, i.e. "test=http://..."
ETCD_INITIAL_CLUSTER="s7kuberma01=http://10.1.51.31:2380,s7kuberma02=http://10.1.51.32:2380,s7kuberma03=http://10.1.51.33:2380"
ETCD_INITIAL_CLUSTER_STATE="new"
ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster"
ETCD_ADVERTISE_CLIENT_URLS="http://10.1.51.32:2379"
```
#### s7kuberma03 etcd

```ruby
# [member]
ETCD_NAME=s7kuberma03
ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
#ETCD_WAL_DIR=""
#ETCD_SNAPSHOT_COUNT="10000"
#ETCD_HEARTBEAT_INTERVAL="100"
#ETCD_ELECTION_TIMEOUT="1000"
ETCD_LISTEN_PEER_URLS="http://10.1.51.33:2380"
ETCD_LISTEN_CLIENT_URLS="http://10.1.51.33:2379,http://localhost:2379"
#ETCD_MAX_SNAPSHOTS="5"
#ETCD_MAX_WALS="5"
#ETCD_CORS=""
#
#[cluster]
ETCD_INITIAL_ADVERTISE_PEER_URLS="http://10.1.51.33:2380"
# if you use different ETCD_NAME (e.g. test), set ETCD_INITIAL_CLUSTER value for this name, i.e. "test=http://..."
ETCD_INITIAL_CLUSTER="s7kuberma01=http://10.1.51.31:2380,s7kuberma02=http://10.1.51.32:2380,s7kuberma03=http://10.1.51.33:2380"
ETCD_INITIAL_CLUSTER_STATE="new"
ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster"
ETCD_ADVERTISE_CLIENT_URLS="http://10.1.51.33:2379"
```

### Enable and start etcd cluster 

```bash
systemctl enable etcd;systemctl start etcd
```

### Check etcd cluster health

```bash
etcdctl cluster-health
```

```bash
member 605710dd5e542748 is healthy: got healthy result from http://10.1.51.33:2379
member 73eb2e8e208fa18f is healthy: got healthy result from http://10.1.51.32:2379
member b7760aa41c6d87b3 is healthy: got healthy result from http://10.1.51.31:2379
cluster is healthy
```
