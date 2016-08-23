---
layout: post
title: "Disableing transparent hugepages (THP) on RHEL/CentOS 6"
description: "Mysql Backup and Restore"
tags: [Linux, kernel]
---

### Environment 
```bach
sed -i 's/quiet/quiet transparent_hugepage=never/' /boot/grub/grub.conf
```
