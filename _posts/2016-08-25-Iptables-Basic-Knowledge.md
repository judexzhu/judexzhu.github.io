---
layout: post
title: "Iptables Basic Knowledge"
description: "Iptables Basic Knowledge"
tags: [Linux, iptables]
---

## location

```bash
/etc/sysconfig/iptables
```

## Start, Stop, Save

```bash
service iptables stop
service iptables start
service iptables restart
service iptables save
```

## Structure

** iptables -> tables -> chains -> rules **

<figure>
	<img src="images/iptables/iptables-table-chain-rule-structure.png" alt="">
</figure>

** There are four kinds built-in tables: Filter, NAT, Mangle and Raw.

### Filter Table
Filter is default table for iptables. It has the following built-in chains.
* INPUT chain - Incoming to firewall. For packets coming to the local server.
* OUTPUT chain - Outgoing from firewall. For packets generated locally and going out of the local server.
* FORWARD chain - Packet for another NIC on the local server . For packets routed through the local server.

### NAT Table
Iptables's NAT table has the following built-in chains.
* PREROUTING chain - Alters packets before routing.i.e Packet translation happens immediately after the packet come to the system (and before routing). This helps to translate the destination ip address of the packets to something that matches the routing on the local server. This is used for DNAT (destination NAT).
* POSTROUTING chain – Alters packets after routing. i.e Packet translation happens when the packets are leaving the system. This helps to translate the source ip address of the packets to something that might match the routing on the desintation server. This is used for SNAT (source NAT).
* OUTPUT chain – NAT for locally generated packets on the firewall.
 
### Mangle table
Iptables’s Mangle table is for specialized packet alteration. This alters QOS bits in the TCP header. Mangle table has the following built-in chains.
* PREROUTING chain
* OUTPUT chain
* FORWARD chain
* INPUT chain
* POSTROUTING chain

### Raw table
Iptable’s Raw table is for configuration excemptions. Raw table has the following built-in chains.
* PREROUTING chain
* OUTPUT chain
