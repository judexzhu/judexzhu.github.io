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

### vi /etc/hosts and add host entry
10.1.51.31  s7kuberma01
10.1.51.32  s7kuberma02
10.1.51.33  s7kuberma03
10.1.51.34  s7kubersla01
10.1.51.35  s7kubersla02

