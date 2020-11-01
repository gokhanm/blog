---
title: "Apache Web Serverda Dosyalara Parolalı Erişim"
date: 2010-05-02T11:38:04+03:00
url: 2010/05/apache-web-serverda-dosyalara-parolali-erisim
categories:
  - nix
tags:
  - apache
  - güvenlik
  - parola
  - htpasswd
---
Apachenin sunduğu güzel imkanlardan biride belirlediğiniz bir dosyadaki klasörlere uzaktan kullanıcıların parolalı bir şekilde erişmesini sağlayabilmesidir.

Bunu yapmak için yapmanız gerekenler çok basit ;

* Bu klasöre girecek olan bir kullanıcının parolasının tutulduğu bir dizin yaratmanız gerekiyor. Bu dizin sistemde herhangi bir yerde olabilir yalnız çoğu makalede /usr/local altında tutulmasını öneriyor. Bu dizini önceden oluşturmak şart.

```sh
# mkdir /usr/local/parola/parolalar
```

* Hangi dizine erişilecek ise ( bu apache hiyerarşisi içinde olmalı ) o dizin altında bir .htaccess dosyası oluşturulmalı. Örneğin /var/www/html/deneme dizininin altına .htaccess dosyasını oluşturalım.

```sh
AuthName               “Korumalı Alan”
AuthType               Basic
AuthUserFile           /usr/local/parola/parolalar
Require                valid-user
```

**/var/www/html/deneme** dizinine girmek isteyen kullanıcı .htaccess dosyasının okunması halinde ekranda küçük bir pencere açılır ve şifre girmenizi ister.

Şimdide deneme dizinine girecekler için şifre belirlemeye geldi. Bunu htpasswd komutu ile yapıyoruz.

```sh
# htpasswd -c /usr/local/parola/parolalar gokhan
Adding password for gokhan
New password : 
Re-type new password :
```

htpasswd kullanırken c parametresini ilk kez kullanıcı şifresi tanımlayacağımız zaman kullanıyoruz ondan sonraki tanımlamalarda kullanılmıyor bu ayrıntı ya dikkat edin.