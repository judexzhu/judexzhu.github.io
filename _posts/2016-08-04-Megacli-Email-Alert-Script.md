---
layout: post
title: "Megacli Email Alert Script"
description: "Megacli Email Alert Script"
tags: [code, linux, megacli]
---

image:
  background: ReservoirDogs_StarWars2-header.jpg

#### MegaRAIDcron.sh
  Create MegaRAIDcron script

~~~ bash
#!/bin/bash
# change to Work Dir
cd /opt/MegaRAID/MegaCli

# Check if there is any error information from the megacli controller information, use keywords "Degraded" or "Failed Disks"
# If it does 
# Save to a file 
# Collect the hostname and IP address and save to the previous file also
# Run "Raidstatus" script to show all the disk information 
# Send to the e-mail you set
if [[ $(/opt/MegaRAID/MegaCli/MegaCli64 -AdpAllInfo -aALL | grep "Degraded" | awk '$3 > 0') ]] || [[ $(/opt/MegaRAID/MegaCli/MegaCli64 -AdpAllInfo -aALL | grep "Failed Disks" | awk '$4 > 0') ]]
then
  echo "Disk Failed:" > degraded.txt
  ./MegaCli64 -AdpAllInfo -aALL | grep "Degraded" >> degraded.txt
  ./MegaCli64 -AdpAllInfo -aALL | grep "Failed Disks" >> degraded.txt
  echo "" >> degraded.txt
  echo "IP ADDRESS:" >> degraded.txt
  ip addr | awk 'sub(/inet /,""){print $1}' >> degraded.txt
  echo "" >> degraded.txt
  raidstatus >> degraded.txt
  cat degraded.txt | mailx -s 'Degraded RAID on '$HOSTNAME your@email.com
fi

# Check if there is any Disk "Error" counts from the megacli phsical disk information, if >0 then send email
# Too much disk errors may means the disk predictable failed
if [[ $(/opt/MegaRAID/MegaCli/MegaCli64 -pdlist -a0 | grep Error | awk '$4 >0') ]]
then
  echo "" > degraded.txt
  echo "IP ADDRESS:" >> degraded.txt
  ip addr | awk 'sub(/inet /,""){print $1}' >> degraded.txt
  echo "" >> degraded.txt
  echo "DISK Error:" >> degraded.txt
  ./MegaCli64 -pdlist -a0 | grep Error >> degraded.txt
  echo "" >> degraded.txt
  raidstatus >> degraded.txt
  cat degraded.txt | mailx -s 'Too Much Disk Error on '$HOSTNAME your@email.com
fi

# Check S.M.A.R.T status from the megacli phsical disk information, if "yes" send email
# "Drive has flagged a S.M.A.R.T alert : Yes" means this disk is predictable failed
if [[ $(/opt/MegaRAID/MegaCli/MegaCli64 -pdlist -a0 | grep flagg | awk '$8 == "Yes"') ]]
then
  echo "" > degraded.txt
  echo "IP ADDRESS:" >> degraded.txt
  ip addr | awk 'sub(/inet /,""){print $1}' >> degraded.txt
  echo "" >> degraded.txt
  echo "S.M.A.R.T Alert:" >> degraded.txt
  ./MegaCli64 -pdlist -a0 | grep flagg >> degraded.txt
  echo "" >> degraded.txt
  raidstatus >> degraded.txt
  cat degraded.txt | mailx -s 'Predict Disk Failed on '$HOSTNAME your@email.com
fi

~~~

#### raidstatus.sh
  Create raidstatus script

~~~ bash
/opt/MegaRAID/MegaCli/MegaCli64 -PdList -aALL | awk -f /opt/MegaRAID/MegaCli/analysis.awk | grep -qEv "*: Online" > /dev/null && echo "Warning: RAID status no longer optimal"
~~~

#### analysis.awk
  Create analysis.awk

~~~ bash
# This is a little AWK program that interprets MegaCLI output

    /Device Id/ { counter += 1; device[counter] = $3 }
    /Firmware state/ { state_drive[counter] = $3 }
    /Inquiry/ { name_drive[counter] = $3 " " $4 " " $5 " " $6 }
    END {
    for (i=1; i<=counter; i+=1) printf ( "Device %02d status is: %s ( %s ) \n", device[i], state_drive[i], name_drive[i]); }
~~~

### raidstatus_console

~~~ bash
/opt/MegaRAID/MegaCli/MegaCli64 -PdList -aALL | awk -f /opt/MegaRAID/MegaCli/analysis.awk
~~~

### saltstack state file for megacli script deploy
~~~ yaml
Megacli:
  pkg.installed:
    - sources:
      - MegaCli: salt://megacli/files/MegaCli-8.07.14-1.noarch.rpm
      - storcli: salt://megacli/files/storcli-1.03.11-1.noarch.rpm
  cmd.script:
    - source: salt://megacli/script/megacli.sh
    - cwd: /root
    - user: root
    - require:
      - pkg: Megacli
      - file: /opt/MegaRAID/MegaCli/analysis.awk
      - file: /opt/MegaRAID/MegaCli/raidstatus_console
/opt/MegaRAID/MegaCli/analysis.awk:
    file:
      - managed
      - source: salt://megacli/files/analysis.awk
      - user: root
      - group: root
      - mode: 0777
      - require:
        - pkg: Megacli
/opt/MegaRAID/MegaCli/raidstatus_console:
    file:
      - managed
      - source: salt://megacli/files/raidstatus_console
      - user: root
      - group: root
      - mode: 0777
      - require:
        - pkg: Megacli
/etc/cron.daily/MegaRAIDcron:
    file:
      - managed
      - source: salt://megacli/files/MegaRAIDcron
      - user: root
      - group: root
      - mode: 0777
      - require:
        - pkg: Megacli
~~~

### Tree
~~~ bash
megacli/
├── files
│   ├── analysis.awk
│   ├── MegaCli-8.07.14-1.noarch.rpm
│   ├── MegaRAIDcron
│   ├── raidstatus_console
│   └── storcli-1.03.11-1.noarch.rpm
├── init.sls
└── script
    └── megacli.sh
~~~
