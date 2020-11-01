---
title: "Chmod Komutu ve Kullanımı"
date: 2009-10-16T12:05:26+03:00
url: 2009/10/chmod-komutu-ve-kullanimi
categories:
  - nix
tags:
  - chmod
  - chmod komutu
  - chmod kullanımı
---

Linux sistemlerde kullanıcıların dosyalara erişim haklarını belirlemek için “chmod” komutu kullanılınır. Chmod un tam karşılığı change moddur.

Linux Kullanıcıları
3 kullanıcı türü bulunmaktadır.

```sh
u – user (dosya veya dizin sahibi, yaratıcısı)
g – group (dosya veya dizinin ait olduğu grup)
o – other (diğerleri, user ve group haricindekiler)
a – all (ugo- user,group, other) (hepsi, herkes dosya ve dizinlere erişebilir.)
```

## CHMOD izinleri ( Chmod Permissions )

Chmod erişim izinleri herzaman rwx şeklinde sıralanmaktadır.

```sh
r – Okuma izni ( Read permission )
w – Yazma izni ( Write permission )
x – Çalıştırma izni ( Execute permission )
```

Bir dizin yada dosyadaki chmod izinlerini görmek için **“ls -all”** komutunu kullanırız.

```sh
drwx------. 53 Gokhan Gokhan 4096 2009-10-10 21:39 .
drwxr-xr-x. 3 root root 4096 2009-08-07 13:27 ..
drwx------. 3 Gokhan Gokhan 4096 2009-06-24 21:12 .adobe
-rw-------. 1 Gokhan Gokhan 11750 2009-10-04 23:51 .bash_history
-rw-r--r--. 1 Gokhan Gokhan 18 2009-04-08 13:46 .bash_logout
-rw-r--r--. 1 Gokhan Gokhan 176 2009-04-08 13:46 .bash_profile
```

```sh
Tür    | Sahibi | Grup    | Diğerleri
(Type) | (user) | (group) | (other)
- -  - | – - –  | – - – - | – - -
```

İlk karakter dosyanın türünü göstermektedir.

Dosya tür çeşitlerini şu şekilde sıralayabiliriz :

```sh
* Normal bir dosya
d Dizin
b Özel blok dosyası
c Özel karakter dosyası
l Sembolik bağlantı dosyası
P Özel isimlendirilmiş pipe dosyası
```

Bazı chmod örnekleri :

* rwx : Okuma, yazma ve çalıştırma erişim izinlerinin hepsi var.
* rw- : Okuma ve yazma izinleri var, çalıştırma için izin yok.
* r-x : Okuma ve çalıştırma izinleri var, yazma için izin yok.
* -wx : Okuma için izin yok, yazma ve çalıştırma izinleri var.
* r–  : Sadece okuma hakkı var.
* -w- : Sadece yazma hakkı var.
* –x  : Sadece çalıştırma hakkı var.
* —   : Hiçbir erişim hakkı yok.

```sh
drwx------. 53 Gokhan Gokhan 4096 2009-10-10 21:39 .
drwxr-xr-x. 3 root root 4096 2009-08-07 13:27 ..
drwx------. 3 Gokhan Gokhan 4096 2009-06-24 21:12 .adobe
-rw-------. 1 Gokhan Gokhan 11750 2009-10-04 23:51 .bash_history
-rw-r--r--. 1 Gokhan Gokhan 18 2009-04-08 13:46 .bash_logout
-rw-r--r--. 1 Gokhan Gokhan 176 2009-04-08 13:46 .bash_profile
```

Yukardaki örneği incelersek;

ilk karakterden sonra 3 blok halinde gelen soldan sağa doğru user, group ve other olarak sıralayabiliriz.
Erişim izinleri herzaman rwx şeklinde verilir eğer rwx yerine “-” işareti varsa o bölüme gelen yerde izin verilmemiş demektir.

```sh
drwxr-xr-x. 3 root root 4096 2009-08-07 13:27 ..
```

Örneğini inceleyelim,

d nin anlamı bunun bir directory yani dizin olduğu anlamındadır.
D den sonra gelen “rwxr” nin anlamıda,
root kullanıcısının bu dizin üzerinde okuma (r) , yazma (w), çalıştırma (x)(execute) haklarının hepsinin verildiğidir.

**“xr”** de root group una üye olan kullanıcıların bu dizin üzerinde x (çalıştırma) ve okama(r) haklarının verildiğidir.

**“x”** in anlamıda bu dizinde diğer kullanıcıların sadece çalıştırma (x) yetkileri vardır.

Bu örnek root (yönetici) kullanıcısının yapabildiklerine ait bir örnek idi. Şimdide sadece kullanıcı olan birinin klasöründe nasıl bir yetkilendirme verilmiş onu inceleyelim.

```sh
drwx------. 53 Gokhan Gokhan 4096 2009-10-10 21:56 Gokhan
```

Bu örnekte gördüğünüz gibi sadece Gokhan kullanıcısının Gokhan klasörüne olan izinleri görebiliyorsunuz. Gördüğünüz gibi sadece user kullanıcısına okuma (r), yazma(w) ve çalıştırma (x) yetkileri verilmiş onun dışında ne Gokhan groupuna üye olan nede diğerlerine herhangi bir hak atanmamış. Bunun anlamı Gokhan groupuna üye olsanız bile bu klasöre hiç bir şekilde giriş yapamazsınız.

Kısaca toparlamak gerekirse,

* drwxrwxrwx : Şeklinde listelenen, d ( dizin ) türünde u, g, o (user, group, other kullanıcılarına) rwx (okuma,yazma,çalıştırma) erişim izinlerinin tümü verilmiş.
* -r–r–r– : Şeklinde listelenen, normal bir dosya türünde her kullanıcıya sadece r (okuma) erişim izni verilmiş.
* -rw-r–r– : Şeklinde listelenen, normal bir dosya türünde sahibine rw (okuma yazma) hakkı verilerek grup ve diğer kullanıcılara sadece r (okuma) erişim izni verilmiş.

Chmod izinleri yularıda anlattığım gibi harflerle olabileceği gibi rakamlarlada olabilir.

```sh
r ( Okuma izni ) = 4
w ( Yazma izni ) = 2
x ( Çalıştırma izni ) = 1
```

000 : r (Okuma hakkı yok), w (Yazma hakkı yok), x (Çalıştırma hakkı yok)
:- – : chmod değeri 0

001 : r (Okuma hakkı yok), w (Yazma hakkı yok), x (Çalıştırma hakkı var)
: – -x : chmod değeri 1

010 : r (Okuma hakkı yok), w (Yazma hakkı var), x (Çalıştırma hakkı yok)
: -w- : chmod değeri 2

011 : r (Okuma hakkı yok), w (Yazma hakkı var), x (Çalıştırma hakkı var)
: -wx : chmod değeri 3

100 : r (Okuma hakkı var), w (Yazma hakkı yok), x (Çalıştırma hakkı yok)
: r- – : chmod değeri 4

101 : r (Okuma hakkı var), w (Yazma hakkı yok), x (Çalıştırma hakkı var)
: r-x : chmod değeri 5

110 : r (Okuma hakkı var), w (Yazma hakkı var), x (Çalıştırma hakkı yok)
: rw- : chmod değeri 6

111 : r (Okuma hakkı var), w (Yazma hakkı var), x (Çalıştırma hakkı var)
: rwx : chmod değeri 7

{{% notice note %}}
Bir dosya ve dizinin chmod erişim iznini dosya sahibi [owner] , üst kullanıcı yetkisine sahip [superuser] ve root hakkına sahip kullanıcılar değiştirebilmektedir.
{{% /notice %}}

## Chmod izinleri için Operatörler

```sh
– İzinleri kaldır ( remove chmod permission )
+ İzinleri ekle ( add chmod permission )
= İzinleri koy ( set chmod permission )
```

Bazı chmod örnekleri :

* chmod +r deneme : deneme dosyasına okuma(r) izni vermiş olduk.
* chmod u=rw,go= deneme : Dosya sahibine okuma ve yazma izni verdik. Grup ve diğerleri için tüm erişim izinlerini kaldırdık.
* chmod +x dosyaismi : Dosyaya tüm kullanıcılar (user,group,other) için çalıştırma izni verdik.
* chmod +rw dosyaismi : Komutu veren user için okuma ve yazma izni grup ve diğerleri için sadece okuma izinlerini verdik.
* chmod 0 deneme: Dosya için tüm kullanıcılara ait erişim izinlerini kaldırdık.
* chmod 666 deneme : Tüm kullanıcılar için okuma ve yazma izni verdik.
* chmod 0755 veya chmod 755 dosya_ismi : Bu komutun iki farklı şekilde yazılmış olması önemli değildir. Yaptığı görev aynıdır dosya sahibine okuma, yazma ve çalıştırma haklarını verirken grup ve diğerleri için de okuma ve çalıştırma hakkı vermektedir.
* chmod ugo-rwx dosyaismi : Dosyanın tüm erişim izinlerini kaldırmış olduk. chmod 0 veya chmod 000 da aynı görevi görür.
* chmod go-rw deneme : Grup ve diğer kullanıcıların dosya üzerindeki okuma ve yazma hakkını kaldırdık.
* chmod –R u+r deneme: deneme isimli dizin altındaki tüm dosyalar ve dizinler için kullanıcısına okuma izni verdik. -R (recursive) parametresi ile ilgili dizin altındaki tüm dosyalar ve dizinlerin chmod erişim izni aynı şekilde değiştirilir.
* chmod a+rwx ve chmod 777 deneme : İki farklı komut fakat işlevleri aynı. Tüm kullanıcıların ilgili dosya üzerinde okuma, yazma ve çalıştırma haklarının olmasını sağlar.
* chmod go+r deneme* : ( * ) joker parametresi ile deneme ile başlayan tüm dosyaların grup ve diğer(other) userler tarafından okunması iznini verir.
* chmod go-rwx * veya chmod 700 * : Bir dizin içindeyken kullanılır. Kullanıldığı dizin içindeki tüm dosyalar ve alt dizinlerde grup ve diğer kullanıcılar için okuma, yazma ve çalıştırma erişim izinleri kaldırılmış olur.

Rakam olayını birkaç örnekle açıklamak isterim.

```sh
drwxrwxr-x. 2 Gokhan Gokhan 4096 2009-10-10 22:36 deneme
```

```sh
$ chmod -wx deneme
 
$ ls -all
dr--r--r--. 2 Gokhan Gokhan 4096 2009-10-10 22:36 deneme
```

Bu örnekte deneme klasöründeki bütün yazma ve çalıştırma izinlerini kaldırdık.

```sh
drwxrwxr-x. 2 Gokhan Gokhan 4096 2009-10-10 22:36 deneme
 ```

```sh
$ chmod u=rw,go= deneme
 
drw-------. 2 Gokhan Gokhan 4096 2009-10-10 22:36 deneme
```

Bu örnektede kullanıcının okuma ve yazma haklarını saklı tutarak çalıştırma hakkını kaldırdık. Ayrıca group ve diğerlerininde çalıştırma, yazma ve okuma haklarınıda kaldırmış olduk.

Kaynak : Osman Atabey & www.TcpSecurity.com
http://www.belgeler.org/man/man1/man1-chmod.html
