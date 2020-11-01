---
title: "Postfix - Mail Başlığına Göre Filtreleme"
date: 2012-06-26T11:56:10+03:00
url: 2012/06/postfix-mail-basligina-gore-filtreleme
categories:
  - Postfix
tags:
  - header checks
  - header filter
  - mail başlığına göre filtreleme
  - postfix
  - spam
  - spam engelleme
  - spam mailler
  - subject
  - subject filter
  - subject header checks
---
Mail kullanımının artması ile birlikte günümüzde internet üzerindeki trafiğin büyük bir kısmını spam mailler oluşturmaktadır.

Mail sunucularının hemen hemen hepsinde spam mailleri engellemek için RBL ( Realtime Blockhole List ) ler kullanılır. Bunlara örnek olarak [Spamhaus](http://www.spamhaus.org/) , [Spamcop](http://spamcop.net/) ve ayrıca greylist olarak [postgrey](http://postgrey.schweikert.ch/) verilebilir.

Postfix’de mail başlığına göre filtreleme yapmak için,

main.cf dosyasının içerisinde bulunan aşağıdaki satırın başındaki “#” işareti kaldırarak aktfi etmeniz gerekir.

```sh
header_checks = regexp:/etc/postfix/maps/header_checks
```
Daha sonra postfix dizininin ( elle derleme esnasında başka bir yere kurulum yapmadı iseniz /etc/postfix in altındadır ) içinde bulunan header_checks dosyasını açarak engellemek istediğiniz mail başlığını yazmanız gerekmektedir. header_checks içeriğinin yazım formatı aşağıdaki gibidir.

```sh
/^HEADER: .*content_to_act_on/ ACTION
```
Örnek engellemek istediğimiz spam maillerin mail başlığı “10 Günde 10 Kilo Verin” ise bunun yazım formatı aşağıdaki gibidir.

```sh
/^Subject: .*10 Günde 10 Kilo Verin/ REJECT
```
{{% notice note %}}
Not: header_checks içerisinde düzenlediğiniz reject kelimeleri büyük harf ve küçük harfe duyarlıdır. Yukarıda yazmış olduğunuz kural eğer mail göndericisi tarafından aşağıki şekilde yazılmışsa postfix bunu engellemeyecektir.
{{% /notice %}}

```sh
10 günde 10 kilo verin
```
header_check dosyasını istediğiniz gibi düzenledikten sonra postfix’i yeniden başlatmanız uyguladığınız kuralın geçerli olması için yeterlidir.

```sh
service postfix restart
```
ya da

```sh
/etc/init.d/postfix restart
```

elle son sürüm derlemesi yaptı iseniz;

```sh
/usr/sbin/postfix reload
```
şeklindedir.
