---
title: "Pure Ftpd Yapilandırması"
date: 2010-06-27T15:16:19+03:00
url: 2010/06/pure-ftpd-yapilandirmasi
categories:
  - nix
tags:
  - pure-ftpd
  - ftp
  - pure-ftpd kurulumu
---
Bu aralar linux ağ yönetimi kitapları okuyorum ve kitap üzerinde kalmasındansa siteye yazıyorumki istediğim zaman istediğim yerden erişebileyim diye. Bunlardan bir taneside pure-ftpd kurulumu ve yapılandırması konusu.

Pure-ftpd’nin özelliklerinden bahsetmek gerekirse. Hızlı ve çok fazla yapılandırma seçeneğine sahip olması imiş :) ( okuduğum kitapda öle yazıyor ) Sanal kota ve LDAP desteği sağlıyor olması.

[Pure-ftpd](http://www.pureftpd.org/) sitesindeki kararlı sürüm indirip kurabilirsiniz ama benim tavisyem [rpmfinder](http://rpmfind.net/linux/rpm2html/search.php?query=pure-ftpd&submit=Search+...&system=&arch=) sitesinden rpm paketini indirip kurmanız yönünde. Bu sayede fazla uğraşmamış olursunuz. :)

Rpm paketini kurduktan sonra **/etc/sysconfig/pure-ftpd** içerisinde yapılandırma dosyaları bulunur. Bu dosyalarda kullanılan parametrelere girmeyeceğim yalnız bunlardan ihtiyacımız olan birkaç tanesine göz atalım.

> –chrooteveryone : Bu parametre ile tüm kullanıcılar kendi ev klasörleri kök dizinleri olur ve başka kullanıcıların klasörlerini göremezler.
> 
> –maxclienstnumber : Bağlantı yapacak en fazla kullanıcı sayısını verir. Bu değer 50 olarak belirlenmiştir. DDOS saldırılarına karşı böle bi sayı > konulmuş
> –maxclientsnumber:50
> 
> –anonymouscantupload: Anonim bağlantılara izin verir yalnız onların sunucuya dosya yüklemelerini kabul etmez.
>
> –maxclientsperip: Bir IP adresinden en fazla kaç bağlantının yapılabileceğini belirler.
>
> –createhomedir: Kullanıcının ev klasörü yoksa otomatik oluşturulmasını sağlar.
>
> –quota: Kullanıcının klasörüne en fazla kaç dosya ve toplam kaç mb dosya yükleyebilecğini gösterir.
> –quota=1000:10 ; en fazla 1000 tane dosya ve 10mb

Pure-ftpd ayrıca sanal kullanıcılar açılabilir. MySQL ve PostgreSQL veritabanlarını kullanabilirsiniz. Ben puredb denilen veritabanını kullanarak kullanıcı açmayı anlatacam. Ayrıca LDAP denetimi yaparakta kullanıcıların bağlanmasını sağlayabilirsiniz.

Puredb ile sanal kullanıcı oluşturmak için /etc/sysconfig/pure-ftpd dosyasına ;

-j -lpuredb:/etc/pureftpd.pdb
satırını eklemeniz gerekir ve sunucuyu yeniden başlatmalısınız.

Şimdi bu kullanıcıların dosyalarını sunucuda nereye atacaklarını ayarlayalım.

/home klasörünün altında ftpdosyalar adında bir klasör açalım.

```sh
# mkdir  /home/ftpdosyalar
# pure-pw useradd abc -u 5000 -g 5000 -d /usr/home/vftp/user
```
Size 2 kere parola soracaktır.En son komutta “abc” adında bir kullanıcı ve grup açtık dosyasıda “/home/ftpdosyalar/abc” oldu.

Ayrıca pure-pw komutunu kullandıktan sonra yaptığınız işlemin görünmesi için aşağıdaki komutu çalıştırmanız gerekir.

```sh
# pure-pw mkdb
```
Artık sunucuya bağlanabilirsiniz. Tabi sunucunuzu başlattıysanız. :)

>
> Ekleme: /var/www/html/domain.com şeklinde home dizini belirlerseniz. Ftp ye bağlanmada sorun çıkarmayacaktır. Yalnız bir dosya ekleme,silme ve  düzenlemede permision  denied hatası alırsanız domain.com dizinine o kullanıcı için izinleri vermeniz gerekiyor. Aşağıdaki komut ile bu işlemi yapabilirsiniz.
> 
```sh
chown -Rc :5000 /var/www/domain.com 
chmod -c g+w /var/www/domain.com
```
> Burada yapdığımız işlem ftp için sanal kullanıcı oluşturuken verdiğimiz Gid yi domain.com klasörüne group izni olarak atamak.
> 
> drwxrwxr-x  7 apache   5000 4096 Jun  30 08:58 domain.com
>

```sh
# service pure-ftpd start
```

```sh
$ftp 192.168.1.3
192.168.1.3 ip adresi benim sanal sunucunun ip adresidir.
```

Ayrıca “abc” kullanıcısının kotasını değiştirmek istiyoruz.

```sh
# pure-pw usermod abc -n 2500 -N 50
```
dediğimizde kullanıcı 2500 dosya ve en fazla 50 mb dosya atabilecektir.

Sunucunuza porgram ile dosya atmak isterseniz Filezilla programını öneririm. Sürükle bırak ile kolaylıkla dosya atabilirsiniz. Bütün sistemlerde çalışır.

Kaynak : Linux Ağ Servisleri – Mustafa Başer – PUSULA

