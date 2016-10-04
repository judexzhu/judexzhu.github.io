---
layout: post
title: "Parted Bash Script for adding /dev/sbd into LVM "
description: "Parted Bash Script for adding /dev/sbd into LVM"
tags: [python]
---

sometimes you install a linux server and have a driver bigger than 2T, so you make two virtual disks and dont want to mount the /dev/sdb to a folder


``` bash
parted --script /dev/sdb mklabel gpt
parted --script /dev/sdb mkpart primary 512 100%
pvcreate -f /dev/sdb1
vgextend vg_centos /dev/sdb1
lvresize -l 100%VG /dev/vg_centos/lv_root
xfs_growfs /dev/mapper/vg_centos-lv_root
df -h
```
