---
title: "Sudoers Listesine Kullanıcı Ekleme"
date: 2014-05-04T11:17:18+03:00
categories:
  - nix
  - Fedora
  - notlar
tags:
  - ds212j
  - root izinleri
  - sudo
  - sudoers
  - synology
  - usermod
  - yetkinlendirme
url: /2014/05/sudoers-listesine-kullanici-ekleme
---
Synology DS212j NAS kullanıyorum. Nas cihazın dropbox mantığında çalışan [Cloud Station](https://www.synology.com/en-us/support/tutorials/529#t3) özelliği mevcut. Nas cihazında oluşturduğunuz bir klasöre, bilgisayarınız ve akıllı telefonunuzdan erişebiliyorsunuz.

Cloud Station senkron yapabilmenizi sağlayan [programı](https://www.synology.com/en-us/support/download/DS212j) fedorada kurmak istediğinizde, kullanıcının bağzı komutları root yetkisine sahipmiş gibi çalıştırabilmeyi istiyor.  O zaman root olarak çalıştırayım dediğinizde herhangi bir işlem yapmayıp, kurulumu gerçekleştirmiyor.

Geçiçi olarak kullanıcıya sudo izinleri verip, kurulumdan sonra bu izni iptal etmek için sudoers dosyasına kullanıcı adı ve verilecek izin düzeyini yazmamız gerekiyor.  Yada bu işlemi kullanıcıyı “wheel” grubuna ekleyerekte yapabilirsiniz.

```sh
# usermod gokhan -a -G wheel
```

Aşağıdaki komut gokhan kullanıcısına tüm izinlerin verildiği anlamına geliyor. Sudoers kullanımına ilişkin ayrıntılara [bakabilirsiniz](http://linux.die.net/man/5/sudoers).

```sh
# echo 'gokhan ALL=(ALL) ALL' >> /etc/sudoers
```

Bu komuttan sonra kullanıcıya geçiş yapıp kurulumu tamamlayabiliyoruz.

Kullanıcı iznini sudoers dosyasından kaldırmak için,

```sh
# visudo
```

komutu ile sudoers dosyasını vi ile açarak editleme işlemi gerçekleştirebiliyoruz. Sudoers dosyasını vi ile direk açmaya çalıştığımızda dosya readonly olduğundan yaptığımız değişiklikleri kaydedemiyoruz.

En altta kullanıcının bulunduğu satırı silebilir yada **"#"** ile devre dışı bırakabiliriz.

usermod komutu ile kaldırmak için de,

```sh
usermod -G wheel gokhan
```