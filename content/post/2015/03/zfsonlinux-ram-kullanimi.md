---
title: "Zfsonlinux Ram Kullanımı"
date: 2015-03-14T14:42:03+03:00
categories:
  - notlar
tags:
  - conf
  - conf parametreleri
  - modprobe.d
  - ram
  - ram kullanımı
  - yapılandırma parametreleri
  - zfs
  - zfs.conf
  - zfsonlinux
url: /2015/03/zfsonlinux-ram-kullanimi
---
Zfsonlinux kurulumunu ve yapılandırmasını önceki yazımda [değinmiştim](/2015/02/zfsonlinux-kurulumu-ve-yapilandirmasi). Bu yazıda da zfs’in tüm sistemdeki boş rami kullanmaması ( yoğun olarak diske yazma işleminin gerçekleşmesi durumunda, sistem swap kullanımına geçebiliyor ) için yapılması gereken düzenlemeden bahsedeceğim.

Modprobe.d altında zfs.conf adında bir dosya oluşturuyoruz. Bu conf dosyasına zfs’in kullanım parametlerini gireceğiz. Bu yazıda zfs’in kullanmasını istediğimi max ram miktarını 8Gb olarak ayarlayacağız.

```sh
# vi /etc/modprobe.d/zfs.conf
 
options zfs zfs_arc_max=8589934592
```

Ayarların aktif olabilmesi için sistemi yeniden başlatmamız gerekiyor.

```sh
# reboot
```

##  Mevcut zfs arc kullanımı

Sistemde mevcut zfs arc durumunu **arcstat.py** komutu yardımıyla yapabiliriz.

```sh
# arcstat.py 1 5
    time  read  miss  miss%  dmis  dm%  pmis  pm%  mmis  mm%  arcsz     c
10:46:52     0     0      0     0    0     0    0     0    0      0  100M
10:46:53     0     0      0     0    0     0    0     0    0      0  100M
10:46:54     0     0      0     0    0     0    0     0    0      0  100M
10:46:55     0     0      0     0    0     0    0     0    0      0  100M
10:46:56     0     0      0     0    0     0    0     0    0      0  100M
```

arcstat.py nin kullanım parametreleri,

```sh
arcstat.py -h
Usage: arcstat [-hvx] [-f fields] [-o file] [-s string] [interval [count]]
        -h : Print this help message
        -v : List all possible field headers and definitions
        -x : Print extended stats
        -f : Specify specific fields to print (see -v)
        -o : Redirect output to the specified file
        -s : Override default field separator with custom character or string
```

##  Konfigurasyonda kullanılabilecek parametreler

zfs.conf dosyasında kullanabileceğimiz parametleri [linkten](http://docs.oracle.com/cd/E26502_01/html/E29022/chapterzfs-1.html#scrolltoc) ayrıntılı olarak inceleyebilirsiniz.