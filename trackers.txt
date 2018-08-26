# updatetrackers
#自动更新aria2trackers,渣脚本自用
#!/bin/bash
deftracurl="https://raw.githubusercontent.com/ngosang/trackerslist/master/trackers_best.txt"
read -t 5 -p "enter your tracksers url,use default addr after 5 sec or click Enter now:" tr
tr=${tr:-$deftracurl}
wget -P /root/ "$tr" -O /root/trackers_ori.txt
awk NF /root/trackers_ori.txt >/root/trackers_or.txt
rm -rf /root/trackers_ori.txt
sed ':a;N;s/\n/,/;t a;' /root/trackers_or.txt > /root/trackers.txt
rm -rf /root/trackers_or.txt
tmp=$(cat /root/trackers.txt) 
trackers="bt-tracker="$tmp
dir=$(find / -type f -iname "aria2.conf")
if [ $(grep "bt-tracker" $dir) ];then
sed -i '/bt-tracker/d' $dir
echo $trackers>>$dir
else
echo $trackers >> $dir
fi
ariadir=$(find / -type f -iname "aria2c"|head -n 1)
if [ $(netstat -apn|grep "aria2") ];then
killall aria2 
sleep 3s&&nohup $ariadir --conf-path=$dir >/tmp/aria2.logs 2>&1  $
else
nohup $ariadir --conf-path=$dir >/tmp/aria2.logs 2>&1  $
exit
fi
