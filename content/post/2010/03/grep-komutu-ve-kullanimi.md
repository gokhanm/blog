---
title: "Grep Komutu ve Kullanımı"
date: 2010-03-14T11:45:48+03:00
url: 2010/03/grep-komutu-ve-kullanimi
categories:
  - nix
tags:
  - grep
  - grep komutu
  - grep kullanımı
---

Grep’in acilimi evrensel düzenli ifade yazicisi dır. (Global Regular Expression Printer). Daha aciklayici olmak gerekirse grep , verilen bir yazidan belirli kriterler dahilinde parcalar cikarir. Basitce , grep bir sablon girmenizi , ardindan yine sizin belirleyeceginiz bir metinde, bu sablona uygun yazilari arar. Belirlenen sablona uygun tüm satirlari listeler. Grep tek başına veya borularla kullanılabilir.

Tek basina kullanıldığında

```sh
$ grep ‘12.00’ /home/david/backup/log.txt
```

Bu komut basit olarak grep in nasil kullanilabilecegini gösteriyor.(Metin dosyalarinin uzantisinin .txt olmasi gibi bir zorunluluk yok).Yukaridaki komut icinde 12.00 bulunan tüm satirlari listeler.
Örnegin bu komut , 12.00 da yapilmis tüm backuplarin bulunmasi icin kullanilabilir (Tabii icinde zamanlarin oldugu log.txt dosyasinin olmasi kosuluyla)

```sh
$ grep -v ‘12.00’ /home/david/backup/log.txt
```

Bu komutla ise icinde 12.00 bulunduran satirlar disindaki tüm satirlari listeler.

```sh
$ grep -l ‘delay’ /code/*.c
```

Bu komut /code dizininde .c uzantili dosyalardan icinde **“delay”** yazisi bulunanlarin adlarini listeler. Bununla sadece dosya isimleri listelenir(Yani satirlar listelenmez..)

```sh
$ grep -w ‘watch\>’ *
```

Bu komut aramayi biraz daha derinlestirir. **“watch”** ile biten kelimelerin oldugu satirlari arar.

Borularla kullanıldığında

```sh
$ ls -l | grep rwxrwxrwx
```

Bildiginiz gibi **“ls -l”** komutu dizin icerigini listeler. **“grep rwxrwxrwx”** kismi ise bize yazma,okuma,calistirma izinlerinin kullanici,grup,digerleri icin verildigi dizinleri listeler. Böylece tüm dizinleri görmektense sadece bu izinlerin oldugu dizinleri görürüz. (Aslinda sadece metin aramasi yapiyoruz. **“ls -l”** nin ciktisini grep komutuna yönlendirerek süzgecten geciriyoruz)

Grep’in ciktisi diger programlara söyle yönlendirilir :


```sh
$ du | grep ‘mp3’ | more

41M ./mp3/01-DANCE AROUND The WORLD/Disc One
44M ./mp3/01-DANCE AROUND The WORLD/Disc Two
84M ./mp3/01-DANCE AROUND The WORLD
68M ./mp3/02-TECHNO MILLENNIUM/Disc One
69M ./mp3/02-TECHNO MILLENNIUM/Disc Two
137M ./mp3/02-TECHNO MILLENNIUM
69M ./mp3/03-CLUB IBIZA/Disc One
70M ./mp3/03-CLUB IBIZA/Disc Two
138M ./mp3/03-CLUB IBIZA
65M ./mp3/04-TRANCE NON-STOP
64M ./mp3/05-MINISTRY ANNUAL 2000/Disc One
65M ./mp3/05-MINISTRY ANNUAL 2000/Disc Two
129M ./mp3/05-MINISTRY ANNUAL 2000
68M ./mp3/06-Paul Oakenfold – Travelling/Disc One
68M ./mp3/06-Paul Oakenfold – Travelling/Disc Two
136M ./mp3/06-Paul Oakenfold – Travelling
57M ./mp3/Depeche_Mode_all/DM 81 Speak And Spell
38M ./mp3/Depeche_Mode_all/DM 82 A Broken Frame
46M ./mp3/Depeche_Mode_all/DM 83 Construction Time Again
48M ./mp3/Depeche_Mode_all/DM 84 Some Great Reward
51M ./mp3/Depeche_Mode_all/DM 86 Black Celebration
58M ./mp3/Depeche_Mode_all/DM 87 Music For The Masses
39M ./mp3/Depeche_Mode_all/DM 89 (Live) 101/Disc A
50M ./mp3/Depeche_Mode_all/DM 89 (Live) 101/Disk B
88M ./mp3/Depeche_Mode_all/DM 89 (Live) 101
44M ./mp3/Depeche_Mode_all/DM 91 Violator
44M ./mp3/Depeche_Mode_all/DM 93 Songs Of Faith And Devotion
54M ./mp3/Depeche_Mode_all/DM 97 ULTRA
43M ./mp3/Depeche_Mode_all/DM 98 The Singles 85-98/CD1
47M ./mp3/Depeche_Mode_all/DM 98 The Singles 85-98/CD2
89M ./mp3/Depeche_Mode_all/DM 98 The Singles 85-98
1.5M ./mp3/Depeche_Mode_all/IMAGES
23M ./mp3/Depeche_Mode_all/MLG 89 Counterfeit e.p
636M ./mp3/Depeche_Mode_all
584M ./mp3/Dj. Alex Techno Part 1 – Mp3
73M ./mp3/DJ_TOP_40_(CD1)
73M ./mp3/DJ_TOP_40_(CD2)
53M ./mp3/EARTH WIND&FIRE.THE_PROMISE
63M ./mp3/FUTURE_TRANCE_(CD1)
65M ./mp3/FUTURE_TRANCE_(CD2)
635M ./mp3/Karisik_Pop
823M ./mp3/mixed
78M ./mp3/soul_saucee
3.7G ./mp3
```
Görüldügü gibi du komutunun ciktilarindan grep yardimiyla “mp3” olanları ayirdik.

```sh
$ grep ` ^ #` /home/david/script1 | more
```

Bu komut /home/david/script1 dosyasindan ‘#’ ile baslayan satirlari gösterir. ‘^#’ bize , #’in ilgili satirin ilk karakteri olmasi gerekliligini anlatir.”more” kismida , ciktinin cok uzun olmasi durumunda sayfalar halinde gösterilebilmesi icindir.

```sh
$ grep -v ` ^ [0-9]` /home/david/backup/log.txt | more
```

Bu komut , **/home/david/backup/log.txt** dosyasinda ilk karakteri 0-9 arasindaki sayilar olan satirlarin disindaki satirlari listeler. More yine ciktiyi sayfalara bölmek icin kullanilir.

{{% notice warning %}}
sablon ifadelerinin tekli tirnak ile kapatilmasi (yukaridaki örneklerde oldugu gibi), yorumlayicinin dogru calismasi icin önemlidir.
{{% /notice %}}

Grep icin bazi önemli parametreler

```sh
-v komutun davranisini tersine cevirir. Örnegin , sablona uygun olanlari göstermesi gerekirken uymayanlari listeler.
-c Standart raporlamayi (yani satirlarin hepsini göstermeyi) keser ve sadece sablona uyan satirlarin sayisini gösterir.
-i Arama sirasinda büyük/kücük harf eslestirmesi yapmaz
-w sablonun baska bir kelime icinde olmayip , kendi basina bir kelime olup olmadigini denetler. Böylece , “bay” kelimesi icin arama yaptiginizda “baywatch” ile ilgili satirlar ekrana gelmez.
-l sablona uygun satirlarin bulundugu dosya adlarini listeler.
-r parametreden sonra verilen dizinin ,alt dizininlerinde de verilen sablona uygun arama yapar. (recursive)
```
