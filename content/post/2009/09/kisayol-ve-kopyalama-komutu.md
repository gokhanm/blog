---
title: "Kisayol ve Kopyalama Komutu"
date: 2009-09-10T12:20:36+03:00
url: 2009/09/kisayol-ve-kopyalama-komutu
categories:
  - nix
tags:
  - cp komutu
  - linux dosya kopyalama
  - linux kısayol oluşturma
  - ls komutu
---
**”ln -s”** komutunu kullanarak bir dosyanın kısayolunu yaratabilirsiniz.

```sh
$ ln -s /media/Yedek /home/Gokhan/Desktop
$ ln -s /media/Film /home/Gokhan/Desktop
```

**“cp”** komutu ile bir dosyayı kopyalayabilirsiniz.

```sh
$ cp /home/Gokhan/Desktop/yeni  /media/Yedek
```

Klasör kopyalamak istediğinizde ise kopyalamak istediğiniz klasörün bulunduğu dizin de iken

```sh
$ cp -r  Linux_dokuman /media/Film
```

komutunu kullanabilirsiniz.
