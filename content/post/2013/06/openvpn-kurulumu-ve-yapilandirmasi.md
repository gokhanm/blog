---
title: "Openvpn Kurulumu Ve Yapılandırması"
date: 2013-06-19T10:57:10+03:00
url: 2013/06/openvpn-kurulumu-ve-yapilandirmasi
categories:
  - nix
tags:
  - centos
  - client
  - epel
  - fedora
  - iptables
  - kurulum
  - openvpn
  - vpn
  - yapılandırma
---
{{% notice info %}}
Kurulum scripti için [tıklayınız](/2017/12/openvpn-kurulum-scripti).
{{% /notice %}}

Bu makalede CentOS 6 üzerinde openvpn kurulumunu, yapılandırılmasını ve vpn’e client olarak bağlanacak olan Fedora üzerinden  ssh ile vpn’e client olarak nasıl bağlanabileceğinize değineceğiz. Kurulumu yaptığım sırada openvpn versiyonu 2.3.1 şeklinde idi. Makalenin devamında göreceğiniz openvpn-2.3.1 dizini, openvpn sürümünün değişmesi durumunda  sizde farklı olarak görünebilir.

RPM üzerinden OpenVPN kurmak için EPEL reposunu sunucuya yüklemeniz gerekiyor.

```sh
#wget http://mirror.vit.com.tr/mirror/Epel/6/x86_64/epel-release-6-8.noarch.rpm
#rpm -Uvh epel-release-6-8.noarch.rpm
#yum install openvpn
```

OpenVPN kurulumu tamamlandıktan sonra sample-config olarak gelen conf dosyalarını /etc/openvpn altına kopyalayacağız.


> Bu noktada küçük bir not düşmem gerekiyor, openvpn’nin yeni versiyonlarında, 
> içerisinde artık easy-rsa scriptleri gelmiyor. Bunun için git reposundan dosyaları indirip, 
> openvpn dizininin içerisine kopyalamanız gerekiyor. Bu makalede easy-rsa nın eski 2x sürümü kullanılmıştır. 
> Yeni 3x sürümünde sertifika oluşturulması tamamen değiştirilmiştir.
> 
> ~~https://github.com/OpenVPN/easy-rsa/tree/master/easy-rsa~~
>
> https://github.com/OpenVPN/easy-rsa-old   — easy-rsa 2x sürümüdür
>
> yeni 3x sürüm için,
> 
> https://github.com/OpenVPN/easy-rsa

Bu bilgilendirmeden sonra openvpn yapılandırmasına devam edelim,

sample-config-files altında bulunan server.conf dosyasını openvpn dizinine kopyalıyoruz,

```sh
# cp /usr/share/doc/openvpn-2.3.1/sample/sample-config-files/server.conf /etc/openvpn
```

Server.conf dosyasında birkaç satırı düzenlememiz gerekiyor, aşağıdaki satır başlarında bulunan **“;”** işaretini kaldırarak satırı aktif ediyoruz.

```sh
#nano /etc/openvpn/server.conf
local 10.10.10.10 # ip adresi openvpn’nin dinleyeceği public ip adresidir.

push “redirect-gateway def1 bypass-dhcp”
push “dhcp-option DNS 8.8.8.8”
push “dhcp-option DNS 8.8.4.4”
user nobody
group nobody
comp-lzo adaptive
```

Server.conf dosyasında sertifika satırları dışında düzenlememiz gereken satırlar bu kadar. Sertifikaları oluşturmak için gereken  easy-rsa scriptlerini git reposundan çekelim,

```sh
#mkdir -p /etc/openvpn/easy-rsa/keys
#mkdir /usr/share/easy-rsa
#cd /usr/share/easy-rsa
#yum install git -y
#git clone https://github.com/OpenVPN/easy-rsa.git
#cp * easy-rsa/2.0 /etc/openvpn/easy-rsa
```

## Easy-rsa aracı ile sertifikaları oluşturma

Sıra geldi easy-rsa scriptlerinde referans olarak kullanılan vars dosyasını düzenleyip, sertifikaları oluşturmaya. Bu dosyayı sertifikaların oluşturulması sırasında sorulan soruların standart olarak gelmesini sağlamak için düzenliyoruz. Sertifika bilgilerini el ile girmeyi düşünüyor iseniz vars dosyasını düzenlemeniz gerekmiyor.

```sh
# nano /etc/openvpn/easy-rsa/vars
export KEY_COUNTRY=”TR”
export KEY_PROVINCE=”IZ”
export KEY_CITY=”Izmir”
export KEY_ORG=”Organization Name”
export KEY_EMAIL=”administrator@example.com”
export KEY_CN=example.com
export KEY_NAME=server
export KEY_OU=server
export KEY_SIZE=2048
```

easy-rsa içerisinde gelen openssl-1.0.0.cnf dosyasını openvpn dizinine kopyalıyoruz.

```sh
# cp /etc/openvpn/easy-rsa/openssl-1.0.0.cnf /etc/openvpn/easy-rsa/openssl.cnf
```

Artık sertifikalarımızı oluşturabiliriz,

```sh
# cd /etc/openvpn/easy-rsa
# source ./vars
# ./clean-all
# ./build-ca
# ./build-key-server server
# ./build-dh
# cd /etc/openvpn/easy-rsa/keys
# cp dh2048.pem ca.crt server.crt server.key /etc/openvpn
```

Sertifikalarımızı oluşturduğumuza göre artık OpenVPN’e sertifikalarımızın yerlerini gösterebiliriz.

```sh
# nano /etc/openvpn/server.conf
ca /etc/openvpn/ca.crt
cert /etc/openvpn/server.crt
key /etc/openvpn/server.key
dh /etc/openvpn/dh2048.pem
```
Sıra geldi client olarak vpn’e bağlanacak olan kullanıcının sertifika bilgilerini oluşturmaya.

```sh
# cd /etc/openvpn/easy-rsa
# ./build-key client
```

## İp Routing işlemi
Sunucu tarafında yapacağımız son bi işlem kaldı, oda vpn kullanıcısı bağlandığında almış olduğu 10.8.0.0/24 ip aralığından ip forwarding ile clientin eth0 üzerinden NAT yaparak internete çıkmasını sağlamak.

```sh
# iptables -t nat -A POSTROUTING -s 10.8.0.0/24 -o eth0 -j MASQUERADE
# iptables-save
```

sysctl.conf dosyasından ip forwarding özelliğini aktif ediyoruz,

```sh
# nano /etc/sysctl.conf
# net.ipv4.ip_forward = 1
```

Artık openvpn’i start ederek, client tarafında yapacağımız ayarlara geçebiliriz.

```sh
# sysctl -p
# /etc/init.d/openvpn start
# chkconfig openvpn on
```

## Client tarafında OpenVPN ayarları
Buradan sonraki işlemleri Fedora client üzerinden devam edeceğiz. Standart olarak fedora üzerinde openvpn kurulumu standart olarak gelir. Bu yüzden direk sample client.conf dosyasını openvpn dizinine kopyalayarak başlayacağız.

```sh
# cp /usr/share/doc/openvpn-2.3.2/sample/sample-config-files/client.conf /etc/openvpn/
# mkdir /etc/openvpn/keys
```
Bu aşamada server tarafında oluşturmuş olduğumuz client crt ve key sertifikalarını fedora üzerine taşımamız gerekiyor. Bu işlemi server üzerinde iken nano ile sertifikalaları açtıktan sonra kopyala-yapıştır ile yapabilir yada filezilla aracılığı ile sftp ile bağlanıp, fedora üzerine çekebilirsiniz. Server üzerinden oluşturduğumuz, fedora üzerinde kopyalamamız gereken dosyalar, ca.crt client.crt client.key şeklindedir. Bu dosyaları /etc/openvpn/keys adında oluşturduğumuz klasöre taşıyoruz. Taşıma işlemi tamamlandıktan sonra sıra openvpn client.conf dosyasına sertifika konumlarını ve bağlanacağımız sunucu bilgilerini tanımlıyoruz.

```sh
# nano /etc/openvpn/client.conf
remote 10.10.10.10. 1194
ca /etc/openvpn/keys/ca.crt
cert /etc/openvpn/keys/client.crt
key /etc/openvpn/keys/client.key
```

Bu işlemlerden sonra aşağıdaki komut root kullanıcısı ile çalıştırıp openvpn servera bağlanmak,

```sh
# openvpn /etc/openvpn/client.conf
```

Komutu uyguladıktan sonra konsolda akan yazıların en sonunda **Initialization Sequence Completed** şeklinde bir satır belirdiğinde, vpn bağlantınız başarılı bir şekilde tamamlanmış demektir.

{{% notice note %}}
Burada küçük bir not düşmem gerekiyor, vpn bağlantısını ssh üzerinden sağladığımız için, çalıştırdığımız komutu iptal ettiğinizde ( Ctrl + x ) vpn bağlantınız kopacaktır.
{{% /notice %}}

Terminal üzerinden VPN bağlantıyı bu şekilde sağlayabileceğiniz gibi Fedora üzerinden GUI aracılığı ile network ayarlarına vpn bağlantı ile birlikte sertifikarı eklemeniz durumda terminal ile uğraşmadan vpn bağlantı sağlayabilirsiniz.

Network ayarlarına girdiğimizde karşımıza aşağıdaki şekilde bir ekran gelir. Buradaki “+” işaretine basarak bir VPN bağlantı ekleyeceğiz,

{{< img src="/images/network1.png" >}}

**“+”** işaretine bastığımızda karşımıza hangi network bağlantısını ekleyeceğimizi soran ekran geliyor. Buradan VPN ni seçiyoruz.

{{< img src="/images/network2.png" >}}

VPN seçeneğini seçtikten sonra karşımıza hangi tür vpn bağlantısı tipini kullanacağımızı soran ekran geliyor. Burada OpenVPN seçeneğini seçiyoruz.

{{< img src="/images/network3.png" >}}

OpenVPN seçeneğini seçtikten sonra aşağıdaki şekilde karşımıza bir ekran geliyor. Burada openvpn sunucusunu kurarken oluşturduğumuz sertifikaları ve sunucumuzun ip adresini gateway bölümüne yazıyoruz.

{{% notice note %}}
Burada bir not düşmem gerekiyor. Sunucudan bilgisayarınıza çektiğiniz sertifikaları home yada başka bir dizinden eklemek istediğinizde izin hatası alabilirsiniz. Bu yüzden sertifikaları “/etc/openvpn” altına taşıyın.
{{% /notice %}}

{{< img src="/images/network4.png" >}}

Bu ekranı openvpn serverı ayarlarken kullandığımız ayarlara göre düzenliyoruz.

{{< img src="/images/network5.png" >}}

Advanced bölümüne tıklayarak daha az trafik harcanması için data sıkıştırmayı aktif ediyoruz.

{{< img src="/images/network6.png" >}}

Ayarlar tamamlandıktan sonra herhangi bir problem olmaması durumunda VPN bağlantısı başarılı bir şekilde sağlandığını vpn işareti ile anlamış oluyoruz.

## OVPN Oluşturma

[OpenVPN Connect programı için ovpn profil dosyası hazırlama](/2014/03/openvpn-icin-ovpn-profil-dosyasi-hazirlama)


