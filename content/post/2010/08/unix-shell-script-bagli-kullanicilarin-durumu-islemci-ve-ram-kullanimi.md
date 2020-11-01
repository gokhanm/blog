---
title: "Unix Shell Script - Bağlı Kullanıcıların Durumu, İşlemci ve Ram Kullanımı"
date: 2010-08-22T14:51:29+03:00
url: 2010/08/unix-shell-script-bagli-kullanicilarin-durumu-islemci-ve-ram-kullanimi
categories:
  - Script
tags:
  - bağlı kullanıcılar
  - işlemci kullanımı
  - ram kullanımı
  - script
  - shell
  - unix
---

Oldukça kullanışlı 3 tane Linux/Unix shell scriptlerini sizinle paylaşmak istiyorum.

Bunlardan birincisi, arkada çalışan programların ne kadar işlemci ve ram kullandıklarını çıktı olarak ekrana veriyor.

```sh
#! /bin/bash
#List processes based on %cpu and memory usage

echo "Start Time" `date`
# By default, it display the list of processes based on the cpu and memory usage #
if [ $# -eq 0 ]
then

    echo "List of processes based on the %cpu Usage"
    ps -e -o pcpu,cpu,nice,state,cputime,args --sort pcpu  # sorted based on %cpu
    echo "List of processes based on the memory Usage"
    ps -e -orss=,args= | sort -b -k1,1n # sorted bases rss value

# If arguements are given (mem/cpu)
else
    case "$1" in
    mem)
     echo "List of processes based on the memory Usage"
      ps -e -orss=,args= | sort -b -k1,1n
     ;;
     cpu)
     echo "List of processes based on the %cpu Usage"
     ps -e -o pcpu,cpu,nice,state,cputime,args --sort pcpu
     ;;
     *)
        echo "Invalid Argument Given \n"
        echo "Usage : $0 mem/cpu"
        exit 1
     esac    

fi
echo "End Time" `date`
exit 0
```

```sh
$./processes.sh
$./processes.sh mem
$./processes.sh cpu
```

şeklinde çalışırarak isterseniz hepsini tekbir çıktıda yada ayrı ayrı çıktılarda alabilirsiniz.

Bir diğer script ise sisteme bağlı olan kullanıcıların ne kadar işlemci kullandıklarını gösteren güzel bir script.

```sh
#! /bin/bash

w > /tmp/a

echo "Total number of unique users logged in currently"
cat /tmp/a|  sed '1,2d' | awk '{print $1}' | uniq | wc -l
echo ""

echo "List of unique users logged in currently"
cat /tmp/a | sed '1,2d'|  awk '{print $1}' | uniq
echo ""

echo "The user who is using high %cpu"
cat /tmp/a | sed '1,2d' | awk   '$7 > maxuid { maxuid=$7; maxline=$0 }; END { print maxuid, maxline }' 

echo ""
echo "List of users logged in and what they are doing"
cat /tmp/a
```

çıktı sonucu ise,

```sh
Total number of unique users logged in currently
1
 
List of unique users logged in currently
Gokhan
 
The user who is using high %cpu
0.10s Gokhan   tty1     :0               10:22    5:44m 14:48   0.10s pam: gdm-passwo
 
List of users logged in and what they are doing
 16:04:13 up  5:44,  3 users,  load average: 0.66, 0.43, 0.31
USER     TTY      FROM              LOGIN@   IDLE   JCPU   PCPU WHAT
Gokhan   tty1     :0               10:22    5:44m 14:48   0.10s pam: gdm-passwo
Gokhan   pts/0    :0.0             12:40    2:02m  0.07s  0.07s bash
Gokhan   pts/1    :0.0             15:49    0.00s  0.05s  0.00s bash
```

3. olarak vereceğim script ise toplam kullanılan ve kullanılmayan ram miktarını veriyor.

```sh
#! /bin/bash

# Total memory space details

echo "Memory Space Details"
free -t -m | grep "Total" | awk '{ print "Total Memory space : "$2 " MB";
print "Used Memory Space : "$3" MB";
print "Free Memory : "$4" MB";
}'

echo "Swap memory Details"
free -t -m | grep "Swap" | awk '{ print "Total Swap space : "$2 " MB";
print "Used Swap Space : "$3" MB";
print "Free Swap : "$4" MB";
}'
```

çıktı sonucuda aşağıdai gibi,

```sh
Memory Space Details
Total Memory space : 6013 MB
Used Memory Space : 1952 MB
Free Memory : 4061 MB
Swap memory Details
Total Swap space : 3999 MB
Used Swap Space : 0 MB
Free Swap : 3999 MB
```

Kaynak: http://www.thegeekstuff.com/