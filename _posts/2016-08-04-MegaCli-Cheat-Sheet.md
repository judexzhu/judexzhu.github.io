###show disk status
~~~ ruby
module Jekyll
  MegaCli -PDlist -aALL -NoLog | egrep 'Slot|state' | awk '/Slot/{if (x)print x;x="";}{x=(!x)?$0:x" -"$0;}END{print x;}' | sed 's/Firmware state://g'
~~~

###Bring bad to good
~~~ ruby
module Jekyll
  MegaCli -PDMakeGood -PhysDrv[E:S] -aN
~~~
