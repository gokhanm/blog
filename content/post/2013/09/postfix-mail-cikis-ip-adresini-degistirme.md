---
title: "Postfix - Mail Çıkıs İp Adresini Değiştirme"
date: 2013-09-15T10:39:00+03:00
url: 2013/09/postfix-mail-cikis-ip-adresini-degistirme
categories:
  - Postfix
tags:
  - blacklist
  - incoming ip
  - mail çıkış ipsi değiştirme
  - mail ip değiştirme
  - outgoing ip
  - postfix ip değiştirme
  - postifx
  - spam
---
Postfix için mail çıkış ( outgoing ) ip adresini değiştirme istediğinizde, google aralamarında en çok karşınıza çıkan main.cf deki inet_interfaces satırına yeni mail çıkış ip adresini yazmak yada smtp_bind_address şeklinde bir satır ekleyerek yeni mail çıkış ip adresini belirtmeniz şeklindedir. Main.cf dosyasında yapmış olduğunuz bu işlem ile postfix  gelecek olan mailleri ( incoming mail ) sadece bu ip adresinden dinleyecektir.  Sonuçta mail çıkış ip adresimiz değişmemiş olacaktır.

Mail çıkış ip adresini değiştirmek için master.cf dosyasındaki aşağıda kısmı,

```sh
smtp      unix  -       -       n	-	-	smtp
# When relaying mail as backup MX, disable fallback_relay to avoid MX loops
relay     unix  -       -       n       - 	-	smtp
        -o smtp_fallback_relay=
```

şeklinde değiştirmelisiniz.

```sh
smtp      unix  -       -       -       -       -       smtp
        -o smtp_bind_address=192.168.0.1
# When relaying mail as backup MX, disable fallback_relay to avoid MX loops
relay     unix  -       -       -       -       -       smtp
        -o smtp_bind_address=192.168.0.1
        -o smtp_fallback_relay=
```

Daha sonra postfix’i yeniden başlatın.

```sh
#service postfix restart
```
