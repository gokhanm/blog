---
title: "Login Sonrası Screen Oturumlarını Görme"
date: 2015-08-30T17:36:13+03:00
categories:
  - notlar
tags:
  - bash
  - bash script
  - bash_profile
  - login
  - screen
  - screen list
  - screen session
url: /2015/08/login-sonrasi-screen-oturumlarini-gorme
---
Daha önceki yazımda screen komutu kullanımından [bahsetmiştim](/2015/08/screen-komutu-kullanimi/). Screen komutunu çok kullandığım için, kapatılmamış birden fazla screen oturumu olabiliyor, çoğunluklada unutuyorum. Sunucuya her login olduğumda screen’nin açık oturumlarını görebilmek için **.bash_profile** dosyasına ( RedHat tabanlı sistemler için)  aşağıdaki scripti ekledim. Bu sayede sunucuya ssh üzerinden her bağlandığımda açık oturumları görebiliyorum.

```sh
if which screen >/dev/null; then
    screen -q -ls
    if [ $? -ge 10 ]; then
        screen -ls
    fi
fi
```

```sh
Last login: Sun Aug 30 05:45:37 2015 from 192.168.10.100
There is a screen on:
	3553.pts-0.test	(Detached)
	3570.pts-0.test	(Detached)
	3561.pts-0.test	(Detached)

3 Socket in /var/run/screen/S-gokhan.
```

Bu yazı vesilesi ile, 30 Ağustos Zafer Bayramımız Kutlu Olsun.

{{< img src="/images/ataturk.jpg" >}}


