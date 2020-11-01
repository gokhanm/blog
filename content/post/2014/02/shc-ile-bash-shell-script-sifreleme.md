---
title: "Shc İle Bash Shell Script Şifreleme"
date: 2014-02-12T13:29:40+03:00
url: 2014/02/shc-ile-bash-shell-script-sifreleme
categories:
  - LKD-Gezegen
  - Scripts
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

[SHC](http://www.datsi.fi.upm.es/~frosal/), binary formatında scriptlerinizi şifrelemeye yarayan bir programcık. Yazdığınız bash scriptleri şifreleyerek kodların okunamamasını sağlayabilirsiniz.

SHC’nin kurulumuna bakacak olursak,

{{% notice note %}}
Sisteminizde “make” kurulu değilse, kurmanız gerekecektir.
{{% /notice %}}

```sh
# wget http://www.datsi.fi.upm.es/~frosal/sources/shc-3.8.9.tgz
# tar -xvf shc-3.8.9.tgz
# cd shc-3.8.9
# make
```
SHC kullanımı;

```sh
shc Usage: shc [-e date] [-m addr] [-i iopt] [-x cmnd] [-l lopt] [-rvDTCAh] -f script
```
Basit bir script oluşturarak shc ile scripti şifreleyelim,

```sh
#!/bin/bash
 
echo  -n "İsminiz nedir?: "
read name
echo "Merhaba $name"
```

```sh
# ./shc -f merhaba.sh
```

```sh
[root@test bash]# ls -l merhaba.sh*
-rw-r--r--. 1 root root    73 Feb 10 23:38 merhaba.sh
-rwx--x--x. 1 root root 11600 Feb 10 23:21 merhaba.sh.x
-rw-r--r--. 1 root root  9485 Feb 10 23:21 merhaba.sh.x.c
```

* merhaba.sh : Şifrelenmemiş shell script
* merhaba.sh.x : Binary formatında şifrelenmiş shell script
* merhaba.sh.x.c : Shell scriptin c formatına çevrilmiş hali.

SHC programını çalıştırdığınızda **“merhaba.sh”** shell scriptini okuyarak ilk önce C formatında **“merhaba.sh.x.c”** dosyasını oluşturuyor. Daha sonra **“merhaba.sh.x.c”** dosyasına bakarak şifrelenmiş binary formatındaki **“merhaba.sh.x”** dosyasını oluşturuyor.

```sh
# ./merhaba.sh.x 
İsminiz nedir?: Gökhan
Merhaba Gökhan
```
**“merhaba.sh.x”** dosyası, shc tarafından çalıştırılabilir olarak oluşturulmakta. Daha sonradan çalıştırma izni vermeniz gerekmiyor. Dosyayı başka bir sunucuya taşımanız gerekiyor ise, çalıştırma izni vermeniz gerekecektir.

**-e** parametresi ile, scripte çalıştırılabilme süresi koyabilirsiniz. Bu sayede scriptin o tarihten sonra çalışmamasını sağlayabilirsiniz. (dd/mm/yyyy)

```sh
# shc -e 09/02/2014 -f merhaba.sh
```

```sh
[root@test bash]# ./merhaba.sh.x 
./merhaba.sh.x: has expired!
Please contact your provider
```

**-m** parametresi ile, scriptin zaman kısıtlaması yüzünden çalışmaması durumunda kiminle nasıl iletişime geçilmesi için gerekli olan notu düşebilirsiniz.

```sh
[root@test bash]# shc-3.8.9/shc -e 09/02/2014 -m "Scriptin zaman kısıtlamaması sebebi ile çalışmamaktadir. Lütfen gokhanmankara.com sitesi üzerinden iletişime geçiniz." -f merhaba.sh
```

```sh
[root@test bash]# ./merhaba.sh.x 
./merhaba.sh.x: has expired!
Scriptin zaman kısıtlamaması sebebi ile çalışmamaktadir. Lütfen gokhanmankara.com sitesi üzerinden iletişime geçiniz.
```

**-r** parametresi ile, güvenlik kısıtlamalarını düşürerek scripti başka bir sunucuya yada farklı bir işletim sistemine taşımanız durumunda çalışmasını sağlayabilirsiniz. **“-r”** parametresi kullanmadan scripti farklı bir işletim sistemli sunucuda çalıştırmayı denediğinizde  hata mesajı alabilirsiniz.

Diğer parametrelerin kullanımı ile ilgili ayrıntılı bilgiyi shc’nin [sitesinden](http://www.datsi.fi.upm.es/~frosal/sources/shc.html) edinebilirsiniz.