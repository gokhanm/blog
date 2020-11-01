---
title: "Nix Sistemlerde Single Mod Şifre Koruması"
date: 2013-04-12T11:33:31+03:00
url: 2013/04/nix-sistemlerde-single-mod-sifre-korumasi
categories:
  - notlar
tags:
  - centos
  - freebsd
  - güvenlik
  - redhat
  - runlevel
  - Scientific Linux
  - şifre koruması
  - single mod şifre koruması
  - single mode
  - SL
---

Single Modu çok sık kullanmasakta, sistemde bir sorun olduğunda, root şifresinin resetlenmesi gerektiğinde sıklıkla baş vurduğumuz bir çalışma seviyesidir (runlevel) Fiziksel olarak erişim sağlanabilen her sunucu single modda açılarak sunucuya izinsiz erişim sağlayıp, sunucu üzerinde her türlü müdale sağlanılabilir.

Bu tür erişimlere bir nebze olsada engel olmak için, sunucunuzun single modda açılması sırasında root şifresinin girilmesini zorunlu hale getirebilirsiniz.

Centos/RedHat/SL için;

**/etc/sysconfig/init** dosyasını herhangi bir editör ile açarak, en altta bulunan SINGLE satırını aşağıdaki şekilde değiştirmek yeterli olacaktır

```sh
SINGLE=/sbin/sulogin
```

Yapılan bu değişiklik sayesinde sunucuyu single modda açmak istediğinizde sizden root şifresini girmenizi isteyecektir.

{{< img src="/images/centos_single_mod.png" >}}

FreeBsd 9.x için,

**/etc/ttys** dosyasındaki **console** ile başlayan satırı aşağıdaki gibi düzenlemeniz yeterli olacaktır.

{{< img src="/images/freebsd_console_insecure.png" >}}

Bu sayede freebsd yi single modda açmak istediğinizde sizden root şifresini isteyecektir.

{{< img src="/images/freebsd_single_mod.png" >}}

{{% notice warning %}}
Şunuda unutmamak gerekir ki, ne kadar şifre koruması yapsanızda, fiziksel olarak erişilebilen bir sunucuya, başka yöntemlerle giriş yapmak her zaman mümkündür.
{{% /notice %}}


