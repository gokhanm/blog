---
title: "Küçük Notlar - Bilgisayar İsmini Değiştirme"
date: 2010-06-23T10:39:10+03:00
url: 2010/06/kucuk-notlar-bilgisayar-ismini-degistirme
categories:
  - notlar
tags:
  - bilgisayarın ismini değiştirme
  - hostname değiştirme
---

Sistemi kurduktan sonra bilgisayarınızın ismini 3 adımda kolayca değiştirebilirsiniz.

* /etc/sysconfig/network dosyasını değiştirme

vi editörü ile **/etc/sysconfig/network** dosyasını açıp “HOSTNAME” yazan yerdeki adı silip yenisini yazın.

```sh
HOSTNAME = yeni.gokhanmankara.com
```
* Konsoldan

```sh
# hostname yeni_hostname
```

tekrar hostname dediğimizde isminin değişmiş olduğunu görüyoruz.

* Hosts dosyasındaki eski ismi yeni ad ile değiştiriyoruz.

```sh
/etc/hosts
```

{{% notice note %}}
Bu uygulama Fedora da denenmiştir. Kullandığınız dağıtımda farklılıklar olabilir.
{{% /notice %}}
