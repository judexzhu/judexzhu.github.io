---
layout: post
title: "How to Use Fuel to Deploy OpenStack  "
description: "How to Use Fuel to Deploy OpenStack"
tags: [Linux, Fuel, Openstack]
---

Fuel is an open-source software application that simplifies the deployment of highly available OpenStack environments, as well as enables you to manage your OpenStack environments after deployment.

## Download Fuel ISO

Download Fuel community version from <a href="https://www.fuel-infra.org/" title="www.fuel-infra.org/">www.fuel-infra.org/</a>

Last Stable Release is **fuel-community-9.0** here is the [ISO.TORRENT][1]

[1]:http://seed.fuel-infra.org/fuelweb-community-release/fuel-community-9.0.iso.torrent?from=landing

## Create a VM for Fuel

### Hardware requirement 

For a production:
  Quad-core CPU
  4 GB RAM
  10 Gigabit network port
  Disk size depends on the number of deployed nodes. All nodes send logs to the Fuel Master node using rsyslog. During installation, Fuel creates a separate partition for /var to store the remote logs and allocates 40% of the disk size to it. Our recommendation for the remote logs partition is 20 GB per node. If you deploy 10 nodes with Fuel, you need to have a (20 x 10) x 2.5 = 500 GB disk for the Fuel Master node.
  IPMI access through an independent management network
  
For a testing environment:
  Dual-core CPU
  2 GB RAM
  1 Gigabit network port
  50 GB disk
  Physical console access
  
  Here is my VM spec: 
