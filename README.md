<p align="center">
  <img width="120px" src="https://upload.wikimedia.org/wikipedia/commons/thumb/3/35/Tux.svg/506px-Tux.svg.png?20220320193426" />
  <h2 align="center"># myBestOneLiners </h2>
  <h3 align="center">And some other tips and tricks..</h3>
</p>

***

My most powerful oneliners will end up here that I have worked on over the years. Do you know more effectively a judgment that I managed to get together, feel free to contribute! I think it's great fun to put together really good one-liners :) Thanks ..

I hope you find something useful.

*** 

### Rounds Slower is better for ssh-keygen -a, as slow as you can tolerate. Timing for different -a values, each measured 20 times:

```sh
for j in 16 32 64 100 150; do
    echo -n "-a $j takes on average";
     for i in {1..20}; do
         ssh-keygen -qa $j -t ed25519 -f test -N test;
         time ssh-keygen -qa $j -N tost -pP test -f test;
         rm test{.pub,};
     done |& grep real | awk -F m '{print $2}' | tr -d s | awk '{sum+=$1} END{print sum/NR}';
done
```

### Print Sensor Data Without Any Extra Tools

```sh
paste <(cat /sys/class/thermal/thermal_zone*/type) <(cat /sys/class/thermal/thermal_zone*/temp) \
| column -s $'\t' -t \
| sed 's/\(.\)..$/.\1°C/'
```

### Print Sensor Data in a for-loop

```sh
for zone in `ls /sys/class/thermal/ | grep thermal_zone`;do
    echo -n "`cat /sys/class/thermal/${zone}/type`: "
    echo `cat /sys/class/thermal/$zone/temp | sed 's/\(.\)..$/.\1°C/'`
done
```

### Print text in flashing colors

```sh
flashing_text () { 
  wuzi='*w*u*s*e*m*a*n*_*p*w*n*z \e[00;34m !';
  for i in {0..59}; do
      echo -ne "\r${wuzi:0:$i}" ;sleep 0.05;
  done };flashing_text;
```

### Print core speeds and also the real speed

```sh
awk -F": " '/cpu MHz\ */ { print "Processor (or core) running speed is: " $2 }' /proc/cpuinfo ; dmidecode | awk -F": " '/Current Speed/ { print "Processor real speed is: " $2 }'
```

### Some distro checker commands I used during the years in my scripts

```sh
distro=$(cat /etc/os-release|head -n 1|cut -d'=' -f2|sed 's/"//g'| awk '{print tolower($0)}')
distro2="$(cat /etc/issue | head -n +1 | awk '{print $1}')"
distro3="$(tr -s ' \011' '\012' < /etc/issue | head -n 1)"
distro4="$(cat /etc/issue | head -n +1 | awk '{print $1}')"
distro5="$(cat /etc/os-release | grep "PRETTY_NAME" | sed 's/PRETTY_NAME=//g' | sed 's/["]//g' | awk '{print $1}')"
awk -F'"' '/NAME/ {print tolower($2)} /NR=2/' /etc/os-release|head -n 1
```
### Get your monitor/screen model 

- Not from myself, unknown author but it's AWESOME!

```sh
while read -r output hex conn; do
    [[ -z "$conn" ]] && conn=${output%%-*}
    echo "# $output $conn   $(xxd -r -p <<< "$hex")"
done < <(xrandr --prop | awk '
    !/^[ \t]/ {
        if (output && hex) print output, hex, conn
        output=$1
        hex=""
    }
    /ConnectorType:/ {conn=$2}
    /[:.]/ && h {
        sub(/.*000000fc00/, "", hex)
        hex = substr(hex, 0, 26) "0a"
        sub(/0a.*/, "", hex)
        h=0
    }
    h {sub(/[ \t]+/, ""); hex = hex $0}
    /EDID.*:/ {h=1}
    END {if (output && hex) print output, hex, conn}
    ' | sort
)

```

### Simple IFS example

```sh
bash -c 'set a b c d; IFS="+-;"; echo "$*"'
```

### Date script for print days, hours, minutes and seconds between the dates

```sh
then=$(date -u -d "2014-10-25 00:00:00" +%s)
now=$(date -u +%s)
date -u -d "2014-01-01 $now sec - $then sec" +"%j days %H hours %M minutes and %S seconds"
```


### Convert a magnet url to a torrent file

```sh
WATCH_DIR="$HOME/downloads"
cd $WATCH_DIR
[[ "$1" =~ xt=urn:btih:([^&/]+) ]] || exit;
echo "d10:magnet-uri${#1}:${1}e" > "meta-${BASH_REMATCH[1]}.torrent"
```

### Check harddrive space and print green after our value is reached or not

```sh
HDFREE=$(df -h|egrep  "[0-9].*\/$"|awk '{print $5}'|sed 's/%//g')

if [[ $HDFREE -lt 50 ]]; then
echo -e "Your shared hard drive is \e[1;31m${HDFREE}%\e[0m full"
else
echo -e "Your shared hard drive is \e[1;32m${HDFREE}%\e[0m full"
fi
```

### How I grab valuable stuff from apk files (extracted)
```sh
grep -EHirn "accesskey|admin|aes|api_key|apikey|checkClientTrusted|crypt|http:|https:|password|pinning|secret|SHA256|SharedPreferences|superuser|token|X509TrustManager|insert into" APKfolder/
```

### Get Sensor Data Without Any Info 

```sh
cat /sys/class/thermal/thermal_zone*/temp
```


## Resize all jpg images to 50% of the original size

```sh
find . -maxdepth 1 -iname "*.jpg" | xargs -L1 -I{} convert -resize 50% "{}" _half/"{}"
```
## Find IOMMU groups

```sh
for d in /sys/kernel/iommu_groups/*/devices/*; do
    n=${d#*/iommu_groups/*}; n=${n%%/*}; 
    printf 'IOMMU Group %s ' "$n"; 
    lspci -nns "${d##*/}"; 
done
```


## We want to open the correct editor based on what environment we are using

    if [ x$DISPLAY != x ]; then
       echo 'set editor="gvim -f"'
     else
       echo 'set editor=vim'
     fi

