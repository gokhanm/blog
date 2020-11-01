---
title: "Nagios Bildirimlerini Smtp Auth İle Göndermek"
date: 2013-04-26T11:23:07+03:00
url: 2013/04/nagios-bildirimlerini-smtp-auth-ile-gondermek
categories:
  - nix
tags:
  - mail gönderimi
  - nagios
  - nagios notification
  - sendemail
  - sendemail kullanımı
  - smtp auth
  - spam
  - spam filter
---
Nagios, standart olarak unix sistemlerde kullanılan mail (sembolik link olarak mailx’e bağlanmıştır)  programı ile bildirimleri stmp auth özelliğini kullanmadan direk olarak commands.cfg dosyasında tanımlanan mail adresini gönderir.

Maillerin terminal üzerinden gönderildiği, spam maillerin hiç olmadığı yada en azından günümüzdeki gibi spam mail gönderimlerinin normal mail gönderimlerine nazaran 2 katından bile fazla olmadığı zamanlarda ( Çağrı Ersen’nin [syslogs](http://www.syslogs.org/2011-2013-araligina-ait-spam-e-posta-istatistikleri/) sitesinde yayınladığı istatistik buna çok güzel bir örnek ), insanların mail gönderimlerinde spama düşme endişeleri olmadığı için rahatlıkla kullandıkları bir uygulama olan mail, bugün  sistem yöneticilerin spam filtrelerine takılma endişelerinden dolayı, farklı arayışlar içine itmiştir. Bu yüzden nagios bildirimlerini sendEmail üzerinden stmp auth ile nasıl gönderecebileceğimizi göreceğiz.

[SendEmail](http://caspian.dotconf.net/menu/Software/SendEmail/#about), perl ile yazılmış, smtp auth özelliği olan ve terminal üzerinden kolaylıkla mail gönderimi yapabileceğiniz ayrıca windows sistemlerde de çalışabilen bir script.

SendEmail’in nagios ile nasıl kullanabileceğimize bakalım,

```sh
# tar -xvf sendEmail-v1.56.tar.gz
```

Tar dosyasını açtığımızda içerisinden kullanacağımız 2 tane dosya çıkar. sendEmail.pl ve sedEmail.pl ye sembolik link olarak gösterilmiş sendEmail dosyası.

İki dosyayıda /usr/local/bin altına kopyalıyoruz.

```sh
# cp /root/sendEmail-v1.56/sendEmail*  /usr/local/bin/
```

Komut üzerinden çalıştığından, sendEmail üzerinden yapılan gönderimleri ve aldığınız hataları görmek isterseniz log dosyasıda kullanabilirsiniz.

```sh
# touch /var/log/sendEmail
# chmod 666 /var/log/sendEmail
```
Nagios, commands.cfg içerisinde tanımlanan komutlar ile karşı sunucudaki servisleri kontrol etmesi için komut gönderir. SendEmail’i commands.cfg dosyasında yer alan ‘notify-host-by-email’ ve ‘notify-service-by-email’ kısımlarına tanıtarak, sendEmail’in smtp auth özelliğini kullanarak nagios bilgirimlerinin gönderilmesini sağlayacağız.

Bundan önce ilk olarak nagios tarafından gönderilecek maillerin hangi mail adresi üzerinden, hangi kullanıcı adı ve şifresi ile gönderileceğini nagios’un resource.cfg dosyasının en altına  tanımlamamız gerekiyor.

```sh
# vi /usr/local/nagios/etc/resource.cfg
```

> $USER5$=nagios@domain.com  # mail adresi
> $USER7$=mail.domain.com:587 # stmp sunucusu ve bağlanılacak port
> $USER9$=nagios@domain.com  # mail kullanıcı adı
> $USER10$=sifre                   # mail adresinin şifresi

Resource dosyasına yaptığımız tanımlamalardan sonra şimdi nagios’a sendEmail üzerinden nasıl mail gönderebileceğini öğreteceğiz.

```sh
# vi /usr/local/nagios/etc/objects/commands.cfg
```

**’notify-host-by-email’** altına ekleyeceğiniz komut
```sh
define command{
command_name    notify-host-by-email
command_line    /usr/bin/printf “%b” “***** Nagios *****\n\nNotification Type: $NOTIFICATIONTYPE$\nHost:
$HOSTNAME$\nState: $HOSTSTATE$\nAddress: $HOSTADDRESS$\nInfo: $HOSTOUTPUT$\n\nDate/Time:
$LONGDATETIME$\n” | /usr/local/bin/sendEmail -s $USER7$ -t $CONTACTEMAIL$ -f $USER5$ -xu $USER9$ -xp $USER10$ -o tls=yes -l
/var/log/sendEmail -u “** $NOTIFICATIONTYPE$ Host Alert: $HOSTNAME$ is $HOSTSTATE$ **” -m “***** Nagios *****\n\nNotification Type:
$NOTIFICATIONTYPE$\nHost: $HOSTNAME$\nState: $HOSTSTATE$\nAddress: $HOSTADDRESS$\nInfo: $HOSTOUTPUT$\\nDate/Time:$LONGDATETIME$\n”
}
```
**’notify-service-by-email’** altına ekleyeceğiniz komut
```sh
define command{
command_name    notify-service-by-email
command_line    /usr/bin/printf “%b” “***** Nagios *****\n\nNotification Type: $NOTIFICATIONTYPE$\n\nService:
$SERVICEDESC$\nHost: $HOSTALIAS$\nAddress: $HOSTADDRESS$\nState: $SERVICESTATE$\n\nDate/Time:
$LONGDATETIME$\n\nAdditional Info:\n\n$SERVICEOUTPUT$” | /usr/local/bin/sendEmail -s $USER7$ -t $CONTACTEMAIL$ -f $USER5$ -xu
$USER9$ -xp $USER10$ -o tls=yes -l /var/log/sendEmail -u “** $NOTIFICATIONTYPE$ Service Alert:
$HOSTALIAS$/$SERVICEDESC$ is $SERVICESTATE$ **” -m “***** Nagios *****\n\nNotification Type: $NOTIFICATIONTYPE$\n\nService:
$SERVICEDESC$\nHost: $HOSTALIAS$\nAddress: $HOSTADDRESS$\nState: $SERVICESTATE$\n\nDate/Time: $LONGDATETIME$\n\nAdditional Info:\n\n$SERVICEOUTPUT$”
}
```

**-o tls=yes** kullanmadığınız zaman aşağıdaki şeklinde hata alabilirsiniz,

{{% notice warning %}}
sendEmail[13416]: NOTICE => Authentication not supported by the remote SMTP server!
{{% /notice %}}

## Örnek SendEmail Kullanımı
```sh
Örnek komut;
# /usr/local/bin/sendEmail -xu nagios@domain.com -xp sifre -f nagios@domain.com -s mail.domain.com:587 -u “UYARI!!” -m “CPU kullanımı arttı” -l /var/log/sendEmail -t admin@domain.com -o tls=yes

-xu : mail kullanıcı adı
-xp : mail kullanıcı adı şifresi
-f : bildirimi gönderecek adres
-s : stmp mail relay
-u : başlık
-m : Mesaj
-l : log çıktısının yazılacağı dosya
-t : bildirimi alacak adres
-o : ekstra seçenekler
```

Yaptığımız değişikliklerden sonra nagios’u restart etmeden, yaptığımız değişiklerde herhangi bir hata olup olmadığını kontrol edelim.

```sh
# /usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg
```
Herhangi bir hata almadı iseniz, artık nagios’u restart ederek yaptığımız değişikliklerin aktif olmasını sağlayabiliriz.

```sh
# service nagios restart
```
SendEmail üzerinden yapılan gönderimlerin loglarını takip etmek için,

```sh
# tail -f /var/log/sendEmail
```
komutunu kullanabilirsiniz.

SendEmail üzerinden yaptığınız gönderimlerde **” -o tls=yes “**  opsiyonunu kullandığımız için ( mail gönderimini yaptığınız sunucu tls’yi zorunlu kılıyor ise, sendEmail üzerinden mail gönderimlerde bu seçeneği de kullanmanız gerekmekte ) hata loglarında aşağıdaki şekilde hata alabilirsiniz.

```sh
sendEmail[13594]: ERROR => No TLS support!  SendEmail can’t load required libraries. (try installing Net::SSLeay and IO::Socket::SSL)
```

Bu hatayı gidermek için, bir alternatif olan perl socket kütüphanesi kurabilirsiniz,

```sh
# yum install perl-IO-Socket-SSL
```

Nagios bildirimleri artık sendEmail üzerinden stmp auth seçeneğini kullanarak contacts.cfg dosyasında belirtlediğiniz mail adres(ler)ine gönderimleri yapacaktır.


