---
title: "Postfix - Mail Kuyruğu Queue İşlemleri"
date: 2012-08-25T11:48:02+03:00
url: 2012/08/postfix-mail-kuyrugu-queue-islemleri
categories:
  - Postfix
tags:
  - kurukta biriken e-mailleri görmek
  - kurukta biriken mailleri görmek
  - kurukta birikme
  - kuyruk işlemleri
  - mail kuyruğu işlemleri
  - mail kuyruk
  - mailq
  - postcat
  - postfix deferred
  - postifx
  - postqueue
  - postsuper
  - queue
  - spam
---

Yoğun olarak kullanılan mail servislerinde, karşı sunucunun cevap vermemesi yada göndermeye çalıştığınız mail boyutunun büyük olmasından kaynaklanan, bunların dışında da mail servisiniz üzerinden spam yapılması sebebi ile mail kuyruğunun şişmesine neden olan sorunlarla  karşılaşılabilmektedir. Bu durumda mailleriniz kuyrukta birikmekte, göndermiş olduğunuz maillerin geç gitmesi gibi sorunlarla karşılaşılmakta.


Kuyrukta biriken mailleri görmek için,

```sh
# mailq
```

Kuyrukta biriken maillerin çokluğu acaba sunucudan spam mı yapılıyor endişesi doğurabilir. Bu durumda kuyrukta biriken maillerin id lerini görerek içeriklerini okuyabiliriz.

```sh
# postqueue -p

-Queue ID- –Size– —-Arrival Time—- -Sender/Recipient——-
9DF7520804A     3726 Sat Aug 25 03:06:41  MAILER-DAEMON
(connect to example.ru[xx.xx.xx.xx]: Connection timed out)
1232sd435@examle.ru
```
Maillerin id lerini gördükten sonra **“9DF7520804A”** idli mailin içeriklerini okumak için,

```sh
# postcat -q 9DF7520804A
```
Bu şekilde kuyrukta biriken maillerin içeriklerini okuyarak birçok soruya cevap elde etmiş olursunuz.

Bundan sonra  yapabileceğiniz işlemlerden bağzıları kuyruğu temizlemek, kuyrukta biriken mailleri zorla göndermeye çalışmak (flush) olabilir.

```sh
# postfix flush
```
ya da

```sh
# postfix -f
```

komutu ile kuyruktaki mailleri zorla göndermeye çalışmanıza rağmen maillier bir türlü karşı tarafa iletilemeyebilir. Bu durumda yapmanız gereken en iyi çözüm kuyruktaki maillerin silinmesi olacaktır.

```sh
# postsuper -d ALL
```
Yukarıdaki komutla tüm kuyrukta bekleyen mailleri silebileceğiniz gibi aşağıdaki komut yardımı ile sadece iletilmesi ertelenmiş mailleri de silebilirsiniz.

```sh
# postsuper -d ALL deferred
```
Yada sadece queue id sini bildiğiniz kuyrukta biriken maili silmek isterseniz,

```sh
# postsuper -d queue_id
```

şeklinde de kullanabilirsiniz.
