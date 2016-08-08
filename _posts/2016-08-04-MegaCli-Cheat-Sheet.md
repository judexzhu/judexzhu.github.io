
### Check Disks Status

~~~ bash
# check disk status
MegaCli -PDlist -aALL -NoLog | egrep 'Slot|state' | awk '/Slot/{if (x)print x;x="";}{x=(!x)?$0:x" -"$0;}END{print x;}' | sed 's/Firmware state://g'
  
~~~

### Bring Bad Disk to Good

~~~ bash

MegaCli -PDMakeGood -PhysDrv[E:S] -aN
~~~

### Bring Unconfiguration(good) to Online

~~~ bash
MegaCli -PDOnline -PhysDrv [E:S] -aN 
~~~

### Scan and Clear Foreign

~~~ bash
#Bring bad to good
MegaCli -CfgForeign -Scan -aALL 
MegaCli -CfgForeign -Clear -aALL
~~~

### Check Disk Information

~~~ bash
MegaCli -PDList -aALL
MegaCli -PDInfo -PhysDrv [E:S] -aALL 

MegaCli64 -AdpAllInfo -aAll
MegaCli64 -LDInfo -Lall -aALL
~~~

### Upgrade Firmware with local file

~~~ bash
MegaCli -adpfwflash -f mr2208fw.rom -a0
~~~

### Raid0 & Raid 1

~~~ bash
Megacli -cfgldadd -r1[252:0,252:1] -a0

Megacli -cfgldadd -r0[252:2] -a0
Megacli -cfgldadd -r0[252:3] -a0
Megacli -cfgldadd -r0[252:4] -a0
Megacli -cfgldadd -r0[252:5] -a0
~~~

### Disk Cache 

~~~ bash
Megacli -LDSetProp -WT -Immediate -L[1-2-3-4] -aAll
Megacli -LDSetProp EnDskCache -L[1-2-3-4] -aAll
~~~
