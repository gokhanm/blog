---
title: "Ncdu Disk Alanı Kullanımı Analiz Aracı"
date: 2013-10-30T15:30:20+03:00
url: 2013/10/ncdu-disk-alani-kullanimi-analiz-araci
categories:
  - nix
  - LKD-Gezegen
  - Scripts
tags:
  -  disk analizi
  - dizin hesaplama
  - du komutu
  - du kullanımı
  - linux disk kullanımı
  - ncdu
  - ncdu kurulumu
---
[Ncdu](http://dev.yorhel.nl/ncdu), komut satırı üzerinden, disk kullanımını gösteren, hızlı, yön tuşları ile dizinler içerisinde gezinebildiğiniz, komut satırı aracıdır. En güzel tarafı “/” dizininde “ncdu” komutunu çalıştırdığınızda, tüm alt dizinleri de hesaplayarak, yön tuşları ile alt dizin ve üst dizinlerde tekrar dizin kullanımını hesapamaya gerek kalmadan gezebilmenize olanak vermesidir. Bu sayede “du” komutunda olduğu gibi, “/” dizinindeki dizinlerin boyutunu hesaplayıp, bir alt dizinde tekrar “du” komutunu çalıştırmanıza gerek kalmamakta.

Ncdu varsayılan ayarlar ile, otomatik olarak her saniyede 10 defa ekranın  dizin kullanımını güncellemekte. Quite mod seçeneği ( -q parametresi ) ile bu durumu her 2 saniyede 1 defa çalışmasını sağlayabiliyorsunuz. Bu sayede trafik kullanımından tasarruf edebilirsiniz.

Ayrıntılı kullanım için [kullanım klavuzuna](http://dev.yorhel.nl/ncdu/man) bakabilirsiniz.

Şuan sitesinde güncel 1.10 sürümü bulunmakta. Centos/RedHat sistemlerde epel reposundan yum komutu ile ncdu-1.7-1 sürümü geliyor,

Centos 6.x  32 bit epel repo kurulumu için,

```sh
#rpm -Uvh http://mirror.vit.com.tr/mirror/Epel/6/i386/epel-release-6-8.noarch.rpm
```

Centos 6.x 64 bit epel repo kurulumu için,

```sh
#rpm -Uvh http://mirror.vit.com.tr/mirror/Epel/6/x86_64/epel-release-6-8.noarch.rpm
```

Epel reposunu kurduktan sonra yum komutu ncdu’yu kurabilirsiniz.

```sh
#yum install ncdu
```

FreeBSD 9.x de ise 1.9 sürümü gelmekte,

```sh
#cd /usr/ports/sysutils/ncdu/ && make install clean
```

ya da

```sh
#pkg_add -r ncdu
```


{{< img src="/images/ncduscan.png" >}}
{{< img src="/images/ncdudone.png" >}}
{{< img src="/images/ncduinfo.png" >}}

{{% notice note %}}
ncdu, du komutunda olduğu gibi, çalıştırdığınız dizin ve alt dizinlere doğru hesapladığını göz ardı etmeyiniz.
{{% /notice %}}

