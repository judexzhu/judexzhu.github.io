---
layout: post
title: "Check Your Linux System Performance in Just One Minute"
description: "Check Your Linux System Performance in Just One Minute"
tags: [Linux]
---
## 10 commands

* uptime
* dmesg | tail
* mpstat -P ALL 1
* pidstat 1
* iostat -xz 1
* free -m
* sar -n DEV 1
* sar -n TCP,ETCP 1
* top


maybe you need 

```bash
 yum -y install procps sysstat
```
