---
title: "Küçük Notlar - Head Ve Tail Komutu"
date: 2010-08-20T14:59:22+03:00
url: 2010/08/kucuk-notlar-head-ve-tail-komutu
categories:
  - notlar
tags:
  - head
  - head kullanımı
  - tail
  - tail kullanımı
---

Bağzı durumlarda büyük boyuttaki dosyaların baştan ilk 6-7 satırını yada sondan 6-7 satırını okumamız gerekebilir. Bu durumlar head ve tail komutlarını kullanarak baştan ve sondan istediğimiz kadar satırın konsolda gözükmesini sağlayabiliriz.

Örnek çıktı dosyası olarak **DIR_COLORS** içindeki ilk 5 satırın çıktısını görüyorsunuz.

```sh
$head -5 /etc/DIR_COLORS
# Configuration file for the color ls utility
# Synchronized with coreutils 8.5 dircolors
# This file goes in the /etc directory, and must be world readable.
# You can copy this file to .dir_colors in your $HOME directory to override
# the system defaults.
```

**tail** komutu ile de son 6 satırın çıktısı görülüyor.

```sh
$ tail -6  /etc/DIR_COLORS
# http://wiki.xiph.org/index.php/MIME_Types_and_File_Extensions
.axa 01;36
.oga 01;36
.spx 01;36
.xspf 01;36
```