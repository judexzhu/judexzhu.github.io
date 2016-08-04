~~~ ruby
# check disk status
  MegaCli -PDlist -aALL -NoLog | egrep 'Slot|state' | awk '/Slot/{if (x)print x;x="";}{x=(!x)?$0:x" -"$0;}END{print x;}' | sed 's/Firmware state://g'
  
~~~


~~~ ruby
#Bring bad to good
  MegaCli -PDMakeGood -PhysDrv[E:S] -aN
~~~
