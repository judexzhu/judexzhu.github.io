---
layout: post
title: "Keepalived and Haproxy"
description: "Basic and simple keepalived and haproxy configuration"
tags: [Linux, haproxy, keepalived]
---


### Just a basic and simple keepalived and haproxy configuration 

OS : CentOS 7

haproxy1: 
    192.168.0.101
haproxy2:
    192.168.0.102

## install and configure keepalived

install keepalived

```bash
yum install -y keepalived
vim /etc/keepalived/keepalived.conf
```

haproxy1 keepalived.conf

```bash
vrrp_script chk_haproxy {
  script "killall -0 haproxy" # check the haproxy process
  interval 2 # every 2 seconds
  weight 2 # add 2 points if OK
}

vrrp_instance VI_1 {
  interface eth0 # interface to monitor
  state MASTER # MASTER on haproxy, BACKUP on haproxy2
  virtual_router_id 51
  priority 101 # 101 on haproxy, 100 on haproxy2
  virtual_ipaddress {
    192.168.0.100 # virtual ip address
  }
  track_script {
    chk_haproxy
  }
}
```

haproxy2 keepalived.conf

```bash
vrrp_script chk_haproxy {
  script "killall -0 haproxy" # check the haproxy process
  interval 2 # every 2 seconds
  weight 2 # add 2 points if OK
}

vrrp_instance VI_1 {
  interface eth0 # interface to monitor
  state BACKUP # MASTER on haproxy, BACKUP on haproxy2
  virtual_router_id 51
  priority 100 # 101 on haproxy, 100 on haproxy2
  virtual_ipaddress {
    192.168.0.100 # virtual ip address
  }
  track_script {
    chk_haproxy
  }
}
```

'killall -0 haproxy' explain :
    If sig is 0, then no signal is sent, but error checking is still performed; this 
can be used to check for the existence of a process ID or process group ID.

Start and enable keepalived

```bash
systemctl enable keepalived
systemctl start keepalived
```

