---
title: "Gnome 3 - Masaüstüne İkon Ekleme"
date: 2011-07-23T14:31:11+03:00
url: 2011/07/gnome-3-masaustune-ikon-ekleme
categories:
  - Gnome
tags:
  - ekleme
  - fedora 15
  - genel
  - gnome 3
  - ikon
  - küçük notlar
---

Bildiğiniz gibi Fedora 15 ile birlikte Gnome 3 gelmekte. Gnome 3 kullanırken en sıkıntı çektiğim nokta Gnome 2.x deki gibi masaüstüne klasör, kısayol vb ikonları ekleyememek oluşuydu.Bunun üstesinden gelmek için Fedora depolarında olan **gnome-tweak-tool** adındaki küçük programı kurmanız gerekiyor.

## Gnome-tweak-tool Kurulumu

Kurulum oldukça basit. Aşağıdaki komut ile fedora deposundan programı kurabiliyorsunuz.

```sh
# su -c "yum install gnome-tweak-tool"
```

Program kurulumundan sonra aplications kısmından

{{< img src="/images/11.png" >}}

search alanına **gnome** yazarak çıkan sonuçlardan **Tweak Advanced Settings** uygulamasını çalıştırıyoruz.

{{< img src="/images/2.png" >}}

Uygulamayı çalıştırdıktan sonra aşağıdaki şekilde karşımıza gelmektedir.

{{< img src="/images/3.png" >}}

##  Masaüstünde Görünecek İkonları Aktif Etme

File Manager sekmesinden **Have file manager handle the desktop** seçeneğini **ON** konumuna getirerek Gnome 2.x de olduğu gibi Create New Document, Create New Folder vb. seşenekleri aktif etmiş oluyoruz.

{{< img src="/images/4.png" >}}

{{< img src="/images/5.png" >}}

Bunun yanında File Manager sekmesindeki diğer seçenekleride ON konumuna getirirsek Bilgisayarım, Geri Dönüşüm Kutusu gibi ikonlarıda aktif etmiş oluyoruz.

{{< img src="/images/6.png" >}}

Masaüstünü kullanmayı sevenlere :)

