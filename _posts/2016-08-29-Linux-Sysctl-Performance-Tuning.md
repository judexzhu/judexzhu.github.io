---
layout: post
title: "Linux Performance Tuning sysctl.conf "
description: "How to Reset the BMC"
tags: [Linux, sysctl]
---

```bash
net.ipv4.ip_forward = 0
```

Enable ip forward, 0 disable, 1 enbale

```bash
net.ipv4.conf.all.rp_filter = 1
```

Enable reverse path filtering 

```bash
net.ipv4.conf.default.accept_source_route = 0
```

The accept_source_route option causes network interfaces to accept packets with the Strict Source Route (SSR) or Loose Source Routing (LSR) option set.

