---
layout: post
title: "Linux Performance Tuning sysctl.conf "
description: "How to Reset the BMC"
tags: [Linux, sysctl]
---

Enable ip forward, 0 disable, 1 enbale
```bash
net.ipv4.ip_forward = 0
```

Enable reverse path filtering 
```bash
net.ipv4.conf.all.rp_filter = 1
```

The accept_source_route option causes network interfaces to accept packets with the Strict Source Route (SSR) or Loose Source Routing (LSR) option set.
```bash
net.ipv4.conf.default.accept_source_route = 0
```

Magic SysRq key is a key sequence that allows some basic commands to be passed directly to the kernel. 
 Here is the list of possible values in /proc/sys/kernel/sysrq:
   0 - disable sysrq completely
   1 - enable all functions of sysrq
  >1 - bitmask of allowed sysrq functions (see below for detailed function
       description):
          2 =   0x2 - enable control of console logging level
          4 =   0x4 - enable control of keyboard (SAK, unraw)
          8 =   0x8 - enable debugging dumps of processes etc.
         16 =  0x10 - enable sync command
         32 =  0x20 - enable remount read-only
         64 =  0x40 - enable signalling of processes (term, kill, oom-kill)
        128 =  0x80 - allow reboot/poweroff
        256 = 0x100 - allow nicing of all RT tasks
```bash
kernel.sysrq = 16
```

core_uses_pid:
The default coredump filename is "core".  By setting
core_uses_pid to 1, the coredump filename becomes core.PID.
If core_pattern does not include "%p" (default does not)
and core_uses_pid is set, then .PID will be appended to
the filename.
```bash
kernel.core_uses_pid = 1
```

The msgmnb tunable specifies the maximum allowable total combined size of all messages queued in a single System V IPC message queue at one time, in bytes.
The default is 16384.
```bash
kernel.msgmnb = 16384
```

The msgmax tunable specifies the maximum allowable size of any single message in a System V IPC message queue, in bytes. msgmax must be no larger than msgmnb (the size of the queue).
```bash
kernel.msgmax = 8192
```

The msgmni tunable specifies the maximum number of system-wide System IPC message queue identifiers (one per queue). The default is 16.
```bash
kernel.msgmni = 7902
```

This file can be used to query and set the run-time limit on the maximum System V IPC shared memory segment size that can be created. It could be max physical memory Byte -1. For example: for 64 GB phyical system, kernel.shmmax = (64*1024*1024*1024)-1 = 68719476735
```bash
kernel.shmmax = 4294967295
```

This file contains the system-wide limits on the total number of pages of System V IPC shared memory.

```bash
kernel.shmall = 268435456
```

~~~bash
kernel.shmall = 268435456
~~~
