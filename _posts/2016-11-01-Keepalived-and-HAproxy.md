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
## install haproxy 

```bash
yum install haproxy
```

### configure haproxy for SELinux and HTTP

```bash
vim /etc/firewalld/services/haproxy-http.xml
```

add

```html
<?xml version="1.0" encoding="utf-8"?>
<service>
<short>HAProxy-HTTP</short>
<description>HAProxy load-balancer</description>
<port protocol="tcp" port="80"/>
</service>
```
assign the correct SELinux context and file permissions to the haproxy-http.xml file.

```bash
cd /etc/firewalld/services
restorecon haproxy-http.xml
chmod 640 haproxy-http.xml
```

### for https

```bash
vim /etc/firewalld/services/haproxy-https.xml
```

```html
<?xml version="1.0" encoding="utf-8"?>
<service>
<short>HAProxy-HTTPS</short>
<description>HAProxy load-balancer</description>
<port protocol="tcp" port="443"/>
</service>
```

```bash
cd /etc/firewalld/services
restorecon haproxy-https.xml
chmod 640 haproxy-https.xml
```
use openssl to generate a self-signed key for ssl
put the certificate and key into a PEM file.

```bash
cat example.com.crt example.com.key > example.com.pem
cp example.com.pem /etc/ssl/private/
```

Configure HAProxy.

```bash
vim /etc/haproxy/haproxy.cfg
```

```bash
frontend http_web *:80
    mode http
    default_backend rgw

frontend rgw-https
  bind <insert vip ipv4>:443 ssl crt /etc/ssl/private/example.com.pem
  default_backend rgw

backend rgw
    balance roundrobin
    mode http
    server  rgw1 10.0.0.71:80 check
    server  rgw2 10.0.0.80:80 check
    
```

Enable/start haproxy

```bash
systemctl enable haproxy
systemctl start haproxy
```

### test

```bash
ip addr show
```

to check the VIP 


