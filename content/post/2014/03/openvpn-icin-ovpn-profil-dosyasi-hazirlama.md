---
title: "OpenVPN için ovpn profil dosyası hazırlama"
date: 2014-03-24T11:25:01+03:00
url: 2014/03/openvpn-icin-ovpn-profil-dosyasi-hazirlama
categories:
  - nix
tags:
  - akıllı telefon
  - android
  - centos
  - ipad
  - iphone
  - openvpn
  - ovpn profil dosyası
  - vpn
---
{{% notice info %}}
OpenVPN kurulum scripti için [tıklayınız](/2017/12/openvpn-kurulum-scripti).
{{% /notice %}}

Vpn seçenekleri içerisinde ücretli, ücretsiz bir çok vpn seçeneği bulunmakta. Bunlardan ücretsiz olanlar insanın gözünde bilgilerimi ( şifre vb. ) kaydediyor mu acaba endişesi yaratırken, ücretli olanlar da ücretsizler gibi aynı endişeleri yaratabilir. Ek olarak bu hizmetler de bir çok müşteriye hizmet verdikleri için trafik vb. limitlemeler mevcut.

Bu tür endişeleri taşımamak ve herhangi bir limitlemeden etkilenmemek için, 5 dolara alacağınız bir sanal sunucuya ( vds ) openvpn kurarak, akıllı telefonunuzdan, bilgisayarınızdan farklı sertifikalar ile endişe duymadan bağlanabilirsiniz. Sunucu kiralarken dikkat etmeniz gereken aylık trafik hakkı  yüksek olan bir sunucu tercih etmek.

[OpenVPN kurulumunu](/2013/06/openvpn-kurulumu-ve-yapilandirmasi) önceki yazılarımda bahsetmiştim. Burada oluşturduğumuz sertifika ve client özelliklerine göre [android](https://play.google.com/store/apps/details?id=net.openvpn.openvpn), [iphone](https://itunes.apple.com/app/openvpn-connect/id590379981) ve [windows](http://openvpn.net/index.php/access-server/download-openvpn-as-sw/357.html) bilgisayarınızda kullanacağınız openvpn connect programı için .ovpn uzantılı openvpn profil dosyası hazırlamayı inceleyeceğiz.

CentOS sunucusuna openvpn kurulumu anlatırken kullanıcı adı ve şifre ile bağlantı gerektirmeden sadece sertifikaları kullanılarak fedora ( openvpn connect programı ile windows için de geçerlidir. ) üzerinden vpn bağlantısını anlatmıştım. Burada ek olarak android akıllı telefonundan bağlanırken ovpn dosyasına kullanıcı adı ve şifreyi de ekleyeceğim. Siz isterseniz bu satırı silerek kullanıcı adı ve şifre istemeden bağlanmasını sağlayabilirsiniz.

{{% notice note %}}
Android telefonlar sadece tun bağlantıyı destekler.
{{% /notice %}}

## Telefonunuz için sertifika oluşturma

Bir client ve server için easy-rsa 2.x ile sertifika oluşturmayı openvpn kurulumu sırasında anlatmış idim. Burada kurulum sırasında kullandığımız vars dosyasındaki seçenekler ile telefon için ayrı bir sertifika oluşturacağız.

```sh
# cd /etc/openvpn/easy-rsa
# source ./vars
# ./pkitool android
```
pkitool scriptini çalıştırdıktan sonra aşağıdaki şekilde satırın en sonunda bir hata alırsanız,

```sh
failed to update database
TXT_DB error number 2
```

Bunun sebebi her oluşturduğunuz ssl sertifikası aşağıdaki konumda kayıt altına alınıyor ve unique bir CN sertifika olabilmesi için sertifika oluşturuyor gibi gözüksede android.crt dosyasının içeriği boş oluyor.

```sh
/etc/ssl/certs
```

Böle bir durumda vars dosyasının KEY_CN satırını ” export KEY_CN=android ” şeklinde düzenleyerek source ve pkitool komutunu tekrar çalıştırın.

android isminde client sertifikasını vars dosyasını baz alarak oluşturduk. keys dosyası içerisine android.crt, andoid.csr ve android.key adında sertifikaları içeren dosya oluşturdu. Bu dosyalardan android.crt ve android.key dosyalarının içindeki sertifikaları kullanacağız.

## VPN için kullanıcı adı ve şifre oluşturma

Ek olarak kullanıcı adı ve şifre bağlantısınıda aktif edecek iseniz, sunucuda bir kullanıcı oluşturmanız gerekiyor. Burada PAM kimlik doğrulama sistemi ile yapacağız. Bunun için sunucuda android isminde kullanıcı oluşturup, şifre koymanız yeterlidir.

```sh
# adduser -M -s /sbin/nologin android
# passwd android
```

## Ovpn dosyasının hazırlanması

Örnek bir .ovpn dosyası aşağıdaki gibidir. **\<ca> \</ca>**, **\<cert> \</cert>** ve **\<key> \</key>** aralarına kurulum sırasında oluşturduğunuz ca.crt, client.crt ve client.key dosyalarının içinde yazan sertifikaları **BEGIN CERTIFICATE** ve **END CERTIFICATE** satırlarınıda içerecek şekilde kopyalayıp, yapıştırın.  Bu dosyayı .ovpn formatında kaydederek, android telefonunuzun SD kartına kaydediyoruz.

```sh
client
proto udp
remote 10.10.10.10
port 1194
dev tun
comp-lzo adaptive
persist-key
persist-tun
auth-user-pass  # bağlantıdan önce kullanıcı adı ve şifre sor
route-metric 1
resolv-retry infinite
nobind
ns-cert-type server
 
<ca>
-----BEGIN CERTIFICATE-----
 
-----END CERTIFICATE-----
</ca>
 
<cert>
-----BEGIN CERTIFICATE-----
 
-----END CERTIFICATE-----
</cert>
 
<key>
-----BEGIN PRIVATE KEY-----
 
-----END PRIVATE KEY-----
</key>
```

android.ovpn dosyasını telefonumuzun SD kartına yükledikten sonra aşağıdaki resimdeki gibi ekliyoruz.

{{< img src="/images/ovpnimport.png" >}}
{{< img src="/images/ovpnimport2.png" >}}

.ovpn dosyasını yüklediğiniz konumdan seçtikten sonra eğer kullanıcı adı ve şifre aktif ise kullanıcı adı ve şifreyi girdikten sonra connect demeniz yeterlidir.

{{% notice note %}}
SD kartınızdan ovpn dosyasını güvenlik sebebi ile silmenizi tavsiye ederim. Ayrıca şifreleme olduğundan çok fazla data alışverişi mevcut. Bu yüzden mobil bağlantı yapmanız durumunda paket limitlerini aşabilirsiniz. Wifi kullanmanızı tavsiye ederim.
{{% /notice %}}
