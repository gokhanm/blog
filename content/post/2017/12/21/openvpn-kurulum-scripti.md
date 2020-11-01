---
title: "Openvpn Kurulum Scripti"
date: 2017-12-21T17:34:24+03:00
categories:
  - nix
tags:
  - openvpn kurulum
  - openvpn
  - openvpn kurulum scripti
  - script
  - vpn
  - fedora
  - centos
  - debian
url: /2017/12/openvpn-kurulum-scripti
---

Daha önceki [makalemde](/2013/06/openvpn-kurulumu-ve-yapilandirmasi) uzun uzun openvpn kurulumunu ve eas-rsa yapılandırmasını anlatmıştım.

Bu işlemleri uzun uzun uğraşmayıp, hazır bir script ile yapmak isteyenler için, **centos**,**fedora** ve **debian** için openvpn kurulum scriptini kullanarak kolayca openvpn, eas-rsa ayarlarını yapıp, belirlediği bir isimde **ovpn** dosyasını elde edebilir.

Aşağıdaki komutlar ile scripti indirip, çalıştırdığınızda size birkaç soru soracaktır,

```sh
# wget https://raw.githubusercontent.com/Nyr/openvpn-install/master/openvpn-install.sh
# chmod +x openvpn-install.sh
# ./openvpn-install.sh
```

İlk önce OpenVPN nin dinleyeceği ip adresini belirtiyorsunuz. ( birden fazla ip adresi tanımlı interface olması durumunda)

```sh
First I need to know the IPv4 address of the network interface you want OpenVPN
listening to.
IP address: 192.168.1.21
```
Daha sonra hangi protokolü kullanacağınızı sorar. Default olarak **UDP** seçilidir. Değişiklik yapmadan enter'a basıp devam ediyoruz.

```sh
Which protocol do you want for OpenVPN connections?
   1) UDP (recommended)
   2) TCP
Protocol [1-2]: 1
```

Protokolü seçtikten sonra OpenVPN'in dinleyeceği portu belirtiyoruz. Bu da default olarak **1194** portudur.

```sh
What port do you want OpenVPN listening to?
Port: 1194
```

Port ayarından sonra size aşağıdaki şekilde hangi DNS leri kullanmak istediğinizi seçmenizi belirtir.

```sh
Which DNS do you want to use with the VPN?
   1) Current system resolvers
   2) Google
   3) OpenDNS
   4) NTT
   5) Hurricane Electric
   6) Verisign
DNS [1-6]: 1
```

Son aşama olarak kurduğunuz OpenVpn'e her  kullanıcının **farklı ovpn** vererek bağlanmasını sağlayabileceğiniz ovpn dosyasının ismini belirtmenizi ister.

```sh
Finally, tell me your name for the client certificate
Please, use one word only, no special characters
Client name: client
```

Tüm işlemler bittikten sonra scripti çalıştırdığınız yerde, belirttiğiniz isimde ovpn dosyası hazırdır. Bu dosyayı kullanarak vpn servera
bağlanabilirsiniz.

Aynı sunucu üzerinde farklı bir kullanıcı için ovpn dosyası oluşturmak isterseniz, tekrar scripti çalıştırdığınızda aşağıdaki gibi bir ekran çıkacaktır.

```sh
Looks like OpenVPN is already installed

What do you want to do?
   1) Add a new user
   2) Revoke an existing user
   3) Remove OpenVPN
   4) Exit
Select an option [1-4]: 
```

* 1 ile yeni bir kullanıcı için **ovpn** dosyası oluşturabilir. 
* 2 ile istediğiniz kullanıcının bağlanmasını iptal edebilir. 
* 3 ile de sunucudan openvpn'i silebilirsiniz.