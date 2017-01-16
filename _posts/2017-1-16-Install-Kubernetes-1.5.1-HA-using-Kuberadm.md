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

```ruby
member 605710dd5e542748 is healthy: got healthy result from http://10.1.51.33:2379
member 73eb2e8e208fa18f is healthy: got healthy result from http://10.1.51.32:2379
member b7760aa41c6d87b3 is healthy: got healthy result from http://10.1.51.31:2379
cluster is healthy
```
now we have an externel etcd cluster "--external-etcd-endpoints=http://10.1.51.31:2379,http://10.1.51.32:2379,http://10.1.51.33:2379" for kubernetes 


## Install Kubeadm on the s7kuberma01

#### repo 
```ruby
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=http://yum.kubernetes.io/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg
       https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF
```

#### selinux
```ruby
setenforce 0
```
#### install the packages and enable the services
```bash
yum install -y docker kubelet kubeadm kubectl kubernetes-cni
systemctl enable docker && systemctl start docker
systemctl enable kubelet && systemctl start kubelet
```
### VIP

now we prepare to init our kubernetes with the kubeadm tools 

but first, for we're trying to deploy an HA env, we have to use the vip for the HA masters which is "10.1.51.30", we will achieve this later with "Keepalived", for right now , let's just add it in the s7kuberma01.

```bash
ip addr add 10.1.51.30/25 dev ens160
```

and check the IP addresses 

```
ip a
```

```ruby
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: ens160: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP qlen 1000
    link/ether 00:0c:29:4d:0a:87 brd ff:ff:ff:ff:ff:ff
    inet 10.1.51.31/25 brd 10.1.51.127 scope global ens160
       valid_lft forever preferred_lft forever
    inet 10.1.51.30/25 scope global secondary ens160
       valid_lft forever preferred_lft forever
    inet6 fe80::20c:29ff:fe4d:a87/64 scope link
       valid_lft forever preferred_lft forever
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN
    link/ether 02:42:c3:25:5c:ad brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 scope global docker0
       valid_lft forever preferred_lft forever
```

you can see ,now we have both the "10.1.51.30" and "10.1.51.31" on the nic card "ens160" now 

### Kubeadm init

```bash
kubeadm init --api-advertise-addresses=10.1.51.30 --external-etcd-endpoints=http://10.1.51.31:2379,http://10.1.51.32:2379,http://10.1.51.33:2379 --pod-network-cidr 10.244.0.0/16
```
3 parameters have been used here

1. API server pionted to the VIP which is 10.1.51.30
2. use the external etcd cluster
3. for we will install network addon flannel later , have to set the pod network  as "10.244.0.0/16"

If everthing runs well, we should see output like this

```ruby
Your Kubernetes master has initialized successfully!

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
    http://kubernetes.io/docs/admin/addons/

You can now join any number of machines by running the following on each node:

kubeadm join --token=2dd145.8c687822f02702f1 10.1.51.30
```

