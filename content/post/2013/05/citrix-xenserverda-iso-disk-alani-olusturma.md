---
title: "Citrix Xenserverda Iso Disk Alanı Oluşturma"
date: 2013-05-02T11:15:23+03:00
url: 2013/05/citrix-xenserverda-iso-disk-alani-olusturma
categories:
  - Sanallastirma
tags:
  - citrix
  - iso alanı
  - iso storage
  - iso storage folder
  - iso storage library
  - xen iso mount etme
  - xencenter
  - xenserver
---
Sürekli Vmware ESX kullanan biri, Citrix XenServer kullanmaya başladığında ilk bocalayacağı nokta, VDSe isoyu nerden mount edeceğim olacaktır. Citrix XenServerda VDSlere iso mount edebilmek için NFS olarak bir disk eklemeli yada Windows File Sharing ( CIFS ) denen iso depo alanı oluşturabilirsiniz. Bunun yanında  mevcut local diskinizde bir klasör oluşturup içerisine iso dosyalarını yükleyerek, bu klasörü bir iso depo alanı olarak da gösterebilirsiniz.

Local diskinizde bir klasör oluşturup, bu klasörü iso depo alanı olarak nasıl kullanacağımıza bakalım.

İlk olarak ssh üzerinden XenServerımıza bağlanıp disk dosya sisteminin nasıl mount edilerek kullanıldığına bakalım.


{{% notice note %}}
Ssh üzerinden bağlanmak için, XenServerı kurduktan sonra ssh remote connection özelliğini aktif etmeniz gerekiyor.
{{% /notice %}}

{{< img src="/images/xenserver_partition_view.png" >}}

Karşımıza 3 tane mount edilmiş dosya sistemi geldi.Bunlardan ilki olan /dev/sda1 XenServer’ın sistem dosyalarının yüklü olduğu kısmı gösterir. Dosya sistemi none olarak görünen  XenServer’ın XenServer Tool denen XenCenter ile VDS arasında, işlemci,ram vb bilgileri görebileceğiniz programı kurabilmeniz için iso olarak mount edilmiştir.  3. ve bizim iso dosyalarımızı burada tutacağımız, dosya sistemi olarak  /dev/mapper/XSLocalEXT şeklinde devam eden, XenServer kurulumunda local storage olarak eklediğimiz, boyut olarak en büyük olan, fiziksel disklerin mount edildiği dosya sistemidir.

Iso klasörünü oluşturacağımız klasöre girelim,

```sh
# cd /var/run/sr-mount/edfee695-47cc-4796-00e6-f54f22d2e71d
```
Isoları yükleyeceğimiz dizini oluşturalım,

```sh
# mkdir iso_alani
```
Oluşturduğumuz iso_alani klasörünü XenCenter üzerinde iso storage alanı olarak tanımlayalım.

```sh
# xe sr-create name-label=ISO_Alani type=iso device-config:location=/var/run/sr-mount/edfee695-47cc-4796-00e6-f54f22d2e71d/iso_alani device-config:legacy_mode=true content-type=iso
```

{{< img src="/images/iso_alani.png" >}}

Herşey hazır, artık iso dosyalarınızı filezilla yada başka bir program aracılığı sftp ile bağlanıp, iso_alani klasörüne yükleyerek XenCenter üzerinde yapacağınız yeni VDS kurulumlarında Disk Drive olarak direk kurulum isosunu seçip, kuruluma başlayabilirsiniz.

{{< img src="/images/Screenshot-from-2013-05-02-205232.png" >}}

{{% notice note %}}
Yüklediğiniz isoların ISO_Alanı bölümünde isolar görünmüyor ise, Rescan diyerek görünmesini sağlayabilirsiniz.
{{% /notice %}}

{{< img src="/images/iso_alani_rescan.png" >}}
