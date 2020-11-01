---
title: "Simge Durumunu Küçült Butonu - Gnome 3"
date: 2011-06-19T14:40:19+03:00
url: 2011/06/simge-durumunu-kucult-butonu-gnome-3
categories:
  - Gnome
tags:
  - fedora 15
  - gnome 3
---
Bildiğiniz gibi gnome 3 ü ilk kurduğunuzda pencerelerde simge durumunu küçük ve ekranı kapla gibi butonlar artık gelmiyor.  Bu butonlardan vaz geçemiyorsanız aşağıdaki yöntemi izleyerek bu butonların tekrar aktif olmasını sağlayabilirsiniz.

ilk yapmamız gereken gnome configuration editörünü kurmak.

```sh
# yum install gconf-editor
```

Kurulum tamamlandıktan sonra system tools un altında  bulunan configuration editor u açıyoruz. Editörü açtıktan sonra desktop->gnome->shell->windows  yolunu izleyerek button_layout a çift tıklamak.

{{< img src="/images/gnome_minimize.jpg" >}}

Açılan pencerede **:close** yazan yere aşağıdaki şekilde değiştiriyoruz.

> :minimize,maximize,close

İşlem tamam tek yapmanız gereken log out olup tekrar girip yapmak.


