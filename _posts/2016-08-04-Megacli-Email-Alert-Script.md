### Copy this into a bash script and set up the crontab

~~~ ruby
#!/bin/bash
# change to Work Dir
cd /opt/MegaRAID/MegaCli

# change if there is any error information from the logical raid, use keywork "Degraed"
# if it does 
# save to a file "degraded.txt"
# collect the hostname and IP address 
# send to e-mail
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
  cat degraded.txt | mailx -s 'Degraded RAID on '$HOSTNAME jude.x.zhu@newegg.com
fi

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
  cat degraded.txt | mailx -s 'Too Much Disk Error on '$HOSTNAME jude.x.zhu@newegg.com
fi

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
  cat degraded.txt | mailx -s 'Predict Disk Failed on '$HOSTNAME jude.x.zhu@newegg.com
fi

~~~
