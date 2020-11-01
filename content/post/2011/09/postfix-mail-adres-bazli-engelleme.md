---
title: "Postfix - Mail Adres Bazlı Engelleme"
date: 2011-09-17T14:00:46+03:00
url : 2011/09/postfix-mail-adres-bazli-engelleme
categories:
  - Postfix
tags:
  - adres
  - block
  - engelleme
  - istenemeyen mail
  - mail
  - mail adresi
  - MTA
  - postfix
  - spam
---
Size sürekli reklam gönderen mail adresini postfix sunucusu üzerinde sizin mail kutunuza gelmeden engellemek oldukça basit.

Bunun için;
```sh
# cd /etc/postfix

# vi sender_access
```
sender_access dosyasına engelemek istediğiniz mail adresini aşağıdaki formatta yazıyorsunuz.

```sh
info@domain.com REJECT
```

Daha sonra postmap komutu ile database ini oluşturuyorsunuz.

```sh
# postmap hash:sender_access
```

Main.cf dosyasına mail geldiğinde sender_access dosyasını kontrol etmesi için ;

```sh
submission_recipient_restrictions = check_sender_access hash:/etc/postfix/sender_access,permit
```
kuralını yazıyoruz.

Değişikliğini yaptığımız main.cf dosyasını kaydedip postfix’e restart atıyoruz.

```sh
#/etc/init.d/postfix restart
```

sender_access dosyasına yazdığınız mail adresinden size mail geldiğinde karşı tarafa **550 Sender address rejected: Access denied** hatası gönderecektir.



