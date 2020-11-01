---
title: "Crontab Kullanımı ve Örnek Crontab Uygulaması"
date: 2010-06-18T10:55:57+03:00
url: 2010/06/crontab-kullanimi-ve-ornek-crontab-uygulamasi
categories:
  - nix
tags:
  - crontab
  - crontab kullanımı
  - crontab örnek
  - cron
---

Linux ve Unix sistemlerde kullanılan crontab, belirlediğiniz bir zaman yada zaman diliminde belirlediğiniz komut, script yada uygulamanın çalışmasını sağlarsınız.

Cron girdilerinin /etc/crontab dosyasının düzenlenerek uygulanır.

* Crontab’ın editlenmesi;

```sh
$crontab -e
```

* Crontab’da yapılacak işlerin listelenmesi;

```sh
$crontab -l 
```

* Crontab’da işlerin silinmesi;

```sh
$crontab -r
```

Bir crontab dosyası aşağıdaki gibi yazılır.

```sh
*  *  *  *  *  /calıstirilacak/komut/yada/script
```

**"*"** ile gösterilen yerler zaman yada tarih değerlerinin kullanılmasında kullanılır. Crontab dosyasına yukarıdaki gibi bir örnek verirseniz. İstenilen komut yada uygulama her dakika bir çalışacak şekilde ayarlanmış olur. 

**"*"** işaretlerinin anlamı;

```sh
.---------------- dakika (0 - 59)
|  .------------- saat (0 - 23)
|  |  .---------- Ayın Günleri (1 - 31)
|  |  |  .------- Ay (1 - 12)
|  |  |  |  .---- Haftanın Günleri (0 - 6) (Pazar=0 ya da 7)
|  |  |  |  |
*  *  *  *  *  Çalıştırılacak komut
```

Görüldüğü gibi ilk yıldız dakikayı ifade eder. 0 – 59 arasında değer alır. 2. yıldız ise saati belirler ve 0 ile 23 arasında bir değer alır. 3. yıldız ayın günlerini ifade eder. 4. yıldız ayları ve son yıldızda haftanın günlerini ifade eder. Burada önemli olan 0 yada 7 değerlerinin pazar gününü ifade ettiğini unutmamak.

Birkaç örnek vermek gerekirse;

* 10 Dakikada Bir Çalışacak Crontab Girdisi

```sh
*/10 * * * * /komut/yada/script

*/10 – Her On Dakika
* – Her Saat
* – Her Gün
* – Her Ay
* – Haftanın Her Günü
```
 
* Hergün öğlen 12 ve akşam 18′de Çalışacak Crontab Girdisi  

```sh
00 12,18 * * * /komut/yada/script

00 – Her Saat (00 Saat başlangıcı)
12,18 – Öğlen 12′de ve Akşam 18′de
* – Her Gün
* – Her Ay
* – Haftanın Her Günü
```

* Sabah 9 Akşam 18 Arasında Her Saat Çalışacak Crontab Girdisi

```sh
00 09-18 * * * /komut/yada/script

00 – Her Saat (00 Saat başlangıcı)
09-18 – Sabah 09′dan Akşam 18′e (09, 10, 11, 12, 13, 14, 15, 16, 17, 18)
* – Her Gün
* – Her Ay
* – Haftanın Her Günü
```

* Hafta İçi Günler Sabah 9 Akşam 18 Arasında Her Saat Çalışacak Crontab Girdisi

```sh
00 09-18 * * 1-5 /komut/yada/script

00 – Her Saat (00 Saat başlangıcı)
09-18 – Sabah 09′dan Akşam 18′e her saat. (09, 10, 11, 12, 13, 14, 15, 16, 17, 18)
* – Her Gün
* – Her Ay
1-5 – Pazartesi (1), Salı(2), Çarşamba(3), Perşembe(4), Cuma(5)
```

Kaynaklar:
[Syslogs](http://www.syslogs.org/)
[Wkipedia](http://en.wikipedia.org/wiki/Cron)