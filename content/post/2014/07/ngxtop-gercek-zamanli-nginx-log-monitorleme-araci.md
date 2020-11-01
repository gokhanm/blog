---
title: "Ngxtop Gercek Zamanlı Nginx Log Monitorleme Aracı"
date: 2014-07-13T15:15:47+03:00
categories:
  - LKD-Gezegen
  - Scripts
tags:
  - gerçek zamanlı monitörleme
  - nginx
  - nginx log monitorleme
  - nginx web server
  - ngxtop
  - real time
url: /2014/07/ngxtop-gercek-zamanli-nginx-log-monitorleme-araci
---
[Ngxtop](https://github.com/lebinh/ngxtop), nginx web serverın loglarını gerçek zamanlı okuyarak, tanımlanan değişkenlere göre sitenize gelen sorguları hızlı ve kolay şekilde terminal üzerinden yorumlamanızı sağlayan bir programdır.

Python ile yazıldığından, python paketlerinin kurulumunda kullanılan pip aracı ile yada easy_install ile  kolayca kurulumunu yapabilirsiniz.

## Ngxtop Kurulumu

Sisteminizde pip kurulu değil ise, ilk önce epel reposunda bulunan python-pip paketini kurmanız gerekiyor.

```sh
# rpm -Uvh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
# yum install python-pip
```

Python-pip paketinin kurulumundan sonra artık pip komutu ile ngxtop programını kurabiliriz.

```sh
# pip install ngxtop
```

Easy_install ile kuracak iseniz, centos’un base reposunda bulunan python-setuptools paketini kurmanız gerekiyor.

```sh
# yum install python-setuptools
# easy_install ngxtop
```

## Ngxtop Kullanımı

```sh
ngxtop [options]
ngxtop [options] (print|top|avg|sum) <var> ...
ngxtop info
ngxtop [options] query <query> ...
```

Bazı kullanım şeçeneklerine bakacak olursa,

* -l <file> : Nginx access log dosyasının tam yolu ( full path )
* -f <format> : Access log formatı ( default: combined )
* –no-follow : ngxtop normalde mevcut log kayıtlarını dikkate almadan anlık olarak log dosyasına yeni yazılacak olan kayıtları dikkate alır. Bu seçeneği kullanarak, ngxtop mevcut log kayıtlarada bakarak çıktıyı vermesini söleriz.
* -t <seconds> : güncelleme sıklığı
* -n <number> : Gösterilecek satır sayısı. ( default değer 10 dur )
* -o <var> :  Sonuçları sıralama kriteri ( default olarak count şeklindedir. )
* -a <exp> …, –a <exp> : Çıktığı ortalama, max, min gibi seçenekler ile filtrelemenizi sağlar. ( exp: sum, avg, min, max  )
* – v : Ayrıntılı çıktı verir.
* -i <filter-expression> : Sadece filter ile eşleşen kayıtları işler.

Ngxtop ile kullanabileceğimiz  buil-in değişkenleri,

* body_bytes_send
* http_referer
* http_user_agent
* remote_addr
* remote_user
* request
* status
* time_local

Eğer nginx’i yum ile kurdu iseniz default olarak conf dosyası etc nin altına kurulduğu için ( /etc/nginx/nginx.conf ) ngxtop komutu verdiğinizde direk bu conf dosyasını okuyarak access log dosyasının yerini bulacaktır.

```sh
#ngxtop
```

Nginx’i elle derleyerek kurduysanız ve etc altına kurmadıysanız,  ngxtop access log dosyasının yerini bulamadığı için ( -l ) parametresi ile log dosyasının yerini göstermeniz gerekecektir.

```sh
# ngxtop -l /usr/local/nginx/log/access.log
```

{{< img src="/images/ngxtop1.jpg" >}}

Built-in değişkenlerini kullanarak çıktı elde etmek için,

```sh
# ngxtop print request http_user_agent remote_addr
```

{{< img src="/images/ngxtop2.jpg" >}}

```sh
# ngxtop top remote_addr
```

{{< img src="/images/ngxtop3.jpg" >}}

```sh
# ngxtop -i 'status == 404' print request status
```

{{< img src="/images/ngxtop4.jpg" >}}

Ngxtop’un en becerikli diğer bir özelliği ise Apache loglarını da yorumlayabilmesi. Apache loglarını yorumlayabilmesi için ngxtop’u aşağıdaki şekilde kullanmanız gerekiyor.

```sh
 # tail -f /var/log/httpd/access.log | ngxtop -f common
```
