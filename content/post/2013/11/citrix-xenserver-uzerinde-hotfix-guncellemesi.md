---
title: "Citrix Xenserver Üzerinde Hotfix Güncellemesi"
date: 2013-11-24T15:22:04+03:00
url: 2013/11/citrix-xenserver-uzerinde-hotfix-guncellemesi
categories:
  - Sanallastirma
  - LKD-Gezegen
tags:
  - citrix
  - citrix xenserver güvenlik güncellemesi
  - citrix xenserver hotfix güncelleme
  - güvenlik güncellemesi
  - xenserver
  - xenserver update
---
Citrix’in XenServer 6.2 ve sonrasında open source olacağını, RedHat daki gibi, XenCenter üzerinden güvenlik güncelleştirmeleri vb işlemlerin lisans satın alınması durumunda yapılabileceğini duyurmasından itibaren, XenCenter üzerinde kullanamadığınız bir çok özelliği kullanma imkanı doğdu. Kullanamadığınız bir özellik olarak XenCenter üzerinden hostların hotfix güncellemelerini yapamıyorsunuz. Eğer lisansınız mevcut ise XenCenter üzerinden Tools > Install Update bölümüne gelerek, indirdiğiniz güncellemeyi ve güncellenecek hosttu seçtikten sonra otomatik güncellemesini sağlayabilirsiniz. Bu tabi lisans almanız durumunda size verilen nimetlerden biri.

Eğer lisansınız yok ise, aşağıdaki resimdeki gibi hosttu seçme imkanı vermiyor.

{{< img src="/images/install-update.png" >}}

XenCenter’dan yapamazsak, bizde ssh üzerinden yaparız diyerek kolları sıvıyoruz.

Check for Updates yada System Alerts bölümünde görünen güncellemeri indirdikten sonra, indirdiğimiz zip dosyasını açarak,  filezilla yada winscp ile XenServer sunucusuna atıp ssh üzerinden “xe” komutu ile güncellemeleri uygulayacağız.

Güncelleme dosyalarını /tmp dizinine attığımızı düşünürsek, host sunucusuna ssh üzerinden bağlanıyoruz. Sunucuya  XS62E002 isimli güncelleştirmeyi uygulayacağız,

```sh
# ssh root@192.168.1.10
# cd /tmp/XS62E002
# xe patch-upload file-name=XS62E002.xsupdate
```

Son uyguladığımız komut bize uuid [alfanümerik](http://tr.wikipedia.org/wiki/Alfan%C3%BCmerik) bir karakter dizisi üretiyor. Bu karakter dizisini patchi uygulamak için tekrar kullanacağız.

{{< img src="/images/install-pending-update.png" >}}
patch-upload komutunu uyguladığınızda xenserver hostunun yanındaki ikon yeşilken sarıya dönüşüyor. Buda bekleyen güncelleştirmelerin olduğunu belirtiyor.

Sıra uuid sini elde ettiğimiz patchi uygulamaya geldi. **\<uuid>** kısmına üretilen uuid karakter dizisini yazmanız gerekiyor.

```sh
# xe patch-pool-apply uuid=<uuid>
```

Aşağıdaki komut ile de uyguladığınız patch ile ilgili ayrıntıları görebilirsiniz.

```sh
# xe patch-list
```

{{< img src="/images/patch-list.png" >}}

Herşey yolunda ise artık restart işlemi uygulayarak güncellemenin aktif olmasını sağlayabiliriz.

```sh
# xe-toolstack-restart
```

Restart işleminden sonra host ile bağlantı kopacağından tekrar bağlanmanız gerekecektir.

Artık hostunuza XS62E002 isimli güncelleme yapılmış demektir.

Kaynak:
[Citrix Support Center](http://support.citrix.com/article/CTX138186)
