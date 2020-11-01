---
title: "Kullanıcıları Varsayılan Olarak Home Dizini Olmadan Oluşturma"
date: 2014-02-14T13:24:30+03:00
url: 2014/02/kullanicilari-varsayilan-olarak-home-dizini-olmadan-olusturma
categories:
  - notlar
tags:
  - adduser
  - centos
  - CREATE_HOME
  - dizin
  - home
  - home dizini oluşturmama
  - ipuçları
  - küçük notlar
  - parametre
---

Centos’da değişik amaçlar için adduser komutu ile başka bir parametre girmeden oluşturduğunuz kullanıcılarda, home dizinini varsayılan olarak oluşturmak istemiyor iseniz, aşağıdaki yöntem ile bunu sağlayabilirsiniz.

```sh
# vi /etc/login.defs
```

login.defs dosyasında **CREATE_HOME** satırı varsayılan olarak yes şeklindedir. **CREATE_HOME no** diyerek kaydederseniz, varsayıılan olarak home dizini yaratılmayacaktır.

```sh
CREATE_HOME no
```

Klasik olarak parametre kullanarak aşağıdaki komut ile “login.defs” dosyasında değişiklik yapmadan, home dizini oluşturmayabilir, ayrıca kullanıcının login olmasını da engelleyebilirsiniz.

```sh
# adduser -M -s /sbin/nologin gokhan
```