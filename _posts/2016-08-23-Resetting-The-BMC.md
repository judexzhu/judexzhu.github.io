---
layout: post
title: "How to Reset the BMC"
description: "How to Reset the BMC"
tags: [Linux, impi, BMC]
---

 Local access or SSH access to the system, run commands with ipmitool and IPMICFG to attempt to regain accessibility to IPMI.

### Resets the management console without rebooting the BMC

```bash
$ ipmitool mc reset warm
```

### Reboots the BMC

```bash
$ ipmitool mc reset cold
```
 
If this fails to restore usability of the interface, you can also attempt a cold reset from Supermicro's IPMICFG.

```bash
$ ipmicfg -nm reset
```

Finally, you can reset the BMC to factory defaults with IPMICFG or ipmitool. Be aware that this will wipe any existing settings on the BMC that you may have set from the web interface, but excludes network settings.

```bash
$ ipmicfg -fd
```

or

```bash
$ ipmitool raw 0x3c 0x40
```

To reset your network settings along with the factory reset, use the following IPMICFG command:

```bash
$ ipmicfg -fde
```

Occasionally your OS may fail to reset the BMC to factory defaults, due to architectural limitations. If you encounter any errors when attempting to reset the BMC, you may need to boot into a DOS environment instead. Attached is a pre-compiled DOS boot image with IPMICFG included, which you can write to a USB or CD.

[Download][1]
[1]:https://siliconmechanics.zendesk.com/hc/en-us/article_attachments/202087645/ipmicfg.iso
