---
title: "Küçük Notlar - Wc Komutu"
date: 2010-08-21T14:56:31+03:00
url: 2010/08/kucuk-notlar-wc-komutu
categories:
  - notlar
tags:
  - wc
  - wc komutu
  - wc kullanımı
---

Bir dosyadaki  kelime, byte ve satırları saymak için **“wc”** komutu kullanılır.  Kullanım şekli;

```sh
wc [OPTION]... [FILE]...
 
şeklindedir.
 
c:  Dosyadaki byteları sayar.
l :  Dosyadaki satırları sayar.
w: Dosyadaki kelimeleri sayar. 
 L: Dosya içindeki en uzun satırın boyutunu gösterir.
 ```

 ```sh
 [Gokhan@localhost ~]$ wc /etc/DIR_COLORS
 
 213  735 4439 /etc/DIR_COLORS
 
[Gokhan@localhost ~]$ wc -c  /etc/DIR_COLORS
4439 /etc/DIR_COLORS
 
[Gokhan@localhost ~]$ wc -l  /etc/DIR_COLORS
213 /etc/DIR_COLORS
 
[Gokhan@localhost ~]$ wc -w  /etc/DIR_COLORS
735 /etc/DIR_COLORS
 
[Gokhan@localhost ~]$ wc -L  /etc/DIR_COLORS
77 /etc/DIR_COLORS
```
