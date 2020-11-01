---
title: "Screen Komutu Kullanımı"
date: 2015-08-02T13:37:30+03:00
categories:
  - notlar
tags:
  - linux screen
  - linux screen komutu
  - rpm
  - screen
  - screen komutu
  - screen kullanımı
  - screen kurulumu
url: /2015/08/screen-komutu-kullanimi
---
Screen komutu, linux sunucunuza ssh bağlantısı yaptıktan sonra birden fazla shell oturumları oluşturmanızı sağlar. Screen komutu kullanarak yeni bir shell oturumu açtığınızda, screen arkada yeni bir process oluşturur, eğer siz oturumdan **Ctrl + a + d** kombinasyonu ile  ayrılsanız (detached ) bile screen komutu arkada oturumu çalıştırmaya devam eder ve daha sonra sizin aynı oturuma bağlanmasınıza olanak sağlar.

Screen komutu en çok, uzun sürecek bir scriptin terminal kapansa bile sonlanmamasını sağlamak için kullanılmaktadır.

## Kurulumu

RPM bazlı sistemlerde, screen programının paketi dağıtımların kendi repolarında mevcut olduğu için ekstra bir repo kurmadan screen programını kurabilirsiniz.

```sh
# yum install screen
```

## Kullanımı

```sh
# screen
```

diyerek yeni bir shell oturumu oluşturuyoruz.

Oluşturduğumuz bu oturumdan ayrılmak ( deatached ) istediğimizde CTRL + a + d kombinasyonunu kullanırız.

Bu sayede ilk ssh bağlantısı yaptığımız shell oturuma düşeriz ve ekranda [ deatached ] yazısı çıkar.

screen komutu ile açılmış oturumları görmek için;

```sh
# screen -list
There are screens on:
20364.pts-0.gokhan (Detached)
20382.pts-0.gokhan (Detached)
2 Sockets in /var/run/screen/S-root.
```

Daha önceden oluşturduğumuz screen oturumuna bağlanmak için screen -list komutundan aldığımız screen id si ile aynı oturuma tekrar bağlanabiliriz.

```sh
# screen -a -r 20364
```

Oturumlar arasında geçmek için,

```sh
CTRL + a + n : Bir sonraki oturuma bağlanır.
 
CTRL + a + p : Bir önceki oturuma geri döner.
```
Eğer oluşturduğunuz oturumu o anda ekranda kitlemek isterseniz;

```sh
CTRL + a + x;
 
Screen used by root <root>.
Password:
```
kombinasyonu ile ekranı kitlemiş olursunuz ve kullanıcı şifresi girilmesi istenecektir.

Oturumu sonlandırmak için, exit diyerek screen oturumunu sonlandırmış olursunuz.

```sh
# exit
```
