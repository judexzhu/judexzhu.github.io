---
layout: post
title: "Disableing transparent hugepages (THP) on RHEL/CentOS 6"
description: "Mysql Backup and Restore"
tags: [Linux, kernel]
---
# How to disable transparent hugepages(THP)

### Environment 
* Red Hat Enterprise Linux (RHEL) 6
* transparent hugepages (THP)
* tuned
* ktune

### Change the kernel setting on **/boot/grub/grub.conf**

```bash
$ sed -i 's/quiet/quiet transparent_hugepage=never/' /boot/grub/grub.conf
```

### reboot your system

```bash
$ reboot
```

### check after reboot

``` bash
$ grep -i never /boot/grub/grub.conf 
    kernel /boot/vmlinuz-2.6.32-358.el6.x86_64 ro root=UUID=a216d1e5-884f-4e5c-859a-6e2e2530d486 rhgb quiet transparent_hugepage=never

$ cat /sys/kernel/mm/redhat_transparent_hugepage/enabled
always [never]
```

# when it is not taking effect

## Issue
* Unable to disable transparent hugepages (THP) even after appending "transparent_hugepage=never" to kernel command line in /boot/grub/grub.conf file.

```bash
$ grep -i never /boot/grub/grub.conf 
    kernel /boot/vmlinuz-2.6.32-358.el6.x86_64 ro root=UUID=a216d1e5-884f-4e5c-859a-6e2e2530d486 rhgb quiet transparent_hugepage=never

$ cat /sys/kernel/mm/redhat_transparent_hugepage/enabled
[always] never

$ grep -i AnonHugePages /proc/meminfo 
AnonHugePages:    206848 kB
```

## Resolution

### Create a customized tuned profile with disabled THP

```bash
$ tuned-adm  active
Current active profile: throughput-performance
Service tuned: enabled, running
Service ktune: enabled, running
$ cd /etc/tune-profiles/
$ cp -r throughput-performance throughput-performance-no-thp

$ sed -ie 's,set_transparent_hugepages always,set_transparent_hugepages never,' \
      /etc/tune-profiles/throughput-performance-no-thp/ktune.sh
$ grep set_transparent_hugepages /etc/tune-profiles/throughput-performance-no-thp/ktune.sh
        set_transparent_hugepages never
$ tuned-adm profile throughput-performance-no-thp
$ cat /sys/kernel/mm/redhat_transparent_hugepage/enabled
always [never]
```

### Alternative: Disable tuned and ktune services.

```bash
$ service tuned stop
$ chkconfig tuned off
$ service ktune stop
$ chkconfig ktune of
```

or

```bash
$ tuned-adm off
```

### Root Cause
* The `ktune` service enables `transparent hugepages` (THP) by default for all profiles.

```bash
# cat /etc/tune-profiles/enterprise-storage/ktune.sh 
#!/bin/sh

. /etc/tune-profiles/functions

start() {
    set_cpu_governor performance
    set_transparent_hugepages always  <<<----
    disable_disk_barriers
    multiply_disk_readahead 4

    return 0
}

stop() {
    restore_cpu_governor
    restore_transparent_hugepages
    enable_disk_barriers
    restore_disk_readahead

    return 0
}

process $@
```

### Diagnostic 
* Verify ktune and tuned services;

```bash
$ chkconfig --list |egrep -i "ktune|tuned"
ktune           0:off   1:off   2:off   3:on    4:on    5:on    6:off
tuned           0:off   1:off   2:on    3:on    4:on    5:on    6:off
```
