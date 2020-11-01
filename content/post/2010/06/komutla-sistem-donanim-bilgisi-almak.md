---
title: "Komutla Sistem Donanim Bilgisi Almak"
date: 2010-06-08T11:19:51+03:00
url: 2010/06/komutla-sistem-donanim-bilgisi-almak
categories:
  - nix
tags:
  - linux donanım bilgisi
  - donanım
  - dmidecode kullanımı
  - dmidecode
---
{{% notice info %}}
Bu komutu “root” kullanıcısı ile kullanabilirsiniz.
{{% /notice %}}

Ana kart üzerindeki RAM yuvası (ing. slot) sayısı, her birisine takılı RAM modülleri ve kapasiteleri, ram yuvalarına takılabilecek azami RAM kapasitesi, ana karta takılı işlemci türü, işlemcinin şu andaki hızı, ana kartın desteklediği azami işlemci saat hızı, BIOS sürümü, ana kart seri numarası, şase seri numarası, donanım bileşenlerinin üretici adları, markaları, modelleri vb uzarda gider…

```sh
$ lspci
```

PCI veri yolunu listeler. PCI veri yoluna takılı bulunan tüm donanımları listeler.

```sh
$ lsusb
```
USB veri yoluna bağlı olan tüm aygıtları, kaç mA akım çektiklerini ayrıntılı bir şekilde dökmektedir. Daha fazla bilgiye ihtiyacınız olduğunda “lsusb -v” biçiminde de kullanabilirsiniz.

```sh
$ cat /proc/cpuinfo
```

İşlemciniz hakkında ayrıntılı bilgiler sunar.

Ayrıca “/proc/acpi”  klasörü altında yer alan tüm klasör ve dosyaları inceleyin. Fanların faal olup olmadıkları ve hangi hızda döndüklerine ilişkin bilgiler, bataryaların şarj mı deşarj mı oldukları, hangi kapasite için tasarlandıkları, son olarak azami doluluk oranının ne olduğu, ısı algılayıcıların gösterdikleri sıcaklık değerleri, notebook ekranlarının açık mı kapalı mı olduğuna dair bilgiler vb bilgileri öğrenebilirsiniz.

```sh
$ hdparm -i /dev/sda
```

komutu diskin marka, model, cache miktarı ve bazı çalışma parametreleri öğrenilebilir.

Daha fazla bilgiye ihtiyacınız varsa /proc ve /sys klasörleri altındaki dosyaları inceleyebilirsiniz.




