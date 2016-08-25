---
layout: post
title: "How to Use the du Command"
description: "How to Use the du Command"
tags: [Linux]
---

We use the `du` command to find out what take the space of the disk in linux systems.

### Most Useful

```bash
$ du -h --max-depth=1 /
77M     /boot
0       /dev
172K    /home
0       /proc
65M     /run
0       /sys
23M     /etc
176K    /root
4.0K    /tmp
463M    /var
976M    /usr
0       /media
0       /mnt
0       /opt
11M     /srv
1.6G    /
```

### Using du With Filters

```bash
du -h --max-depth=1 / | sort -n
0       /dev
0       /media
0       /mnt
0       /opt
0       /proc
0       /sys
1.6G    /
4.0K    /tmp
11M     /srv
23M     /etc
65M     /run
77M     /boot
172K    /home
176K    /root
463M    /var
976M    /usr
```
