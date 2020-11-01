---
title: "Bonding Network Interface Kullanımı"
date: 2014-10-25T11:05:13+03:00
categories:
  - notlar
tags:
  - Aggregate Bond
  - bond interface
  - fault tolerant
  - master slave mod
  - network ayarları
  - round-robin bond
url: /2014/10/bonding-network-interface-kullanimi
---
RedHat tabanlı  işletim sistemlerinde iki veya daha fazla ethernet interface olması durumunda bonding network interface kullanarak, ethernetlerin load balance bond, round-robin bond ya da  fault tolerant bond şeklinde çalışması sağlanabilmektedir. Bu sayede bir interface down olması durumunda sunucuya erişim ikinci interface üzerinden devam edecektir.

Sunucumuzun bond interface ayarlarını aşağıdaki ip adresine göre yapacağız.

{{< img src="/images/ip_conf.png" >}}

## Bond Interface Oluşturma

```sh
# vi /etc/sysconfig/network-scripts/ifcfg-bond0
```

```
DEVICE=bond0
IPADDR=172.16.41.10
NETMASK=255.255.255.0
GATEWAY=172.16.41.1
ONBOOT=yes
BOOTPROTO=none
USERCTL=no
```

##  Eth0 ve Eth1 Interfacelerin Düzenlenmesi

```sh
#vi /etc/sysconfig/network-scripts/ifcfg-eth0
```

```sh
DEVICE=eth0
ONBOOT=yes
BOOTPROTO=none
HWADDR=00:1F:29:E6:EB:2A
USERCTL=no
MASTER=bond0
SLAVE=yes
```

```sh
#vi /etc/sysconfig/network-scripts/ifcfg-eth1
```

```sh
DEVICE=eth1
ONBOOT=yes
BOOTPROTO=none
HWADDR=00:26:55:35:24:FF
USERCTL=no
MASTER=bond0
SLAVE=yes
```

{{% notice info %}}
eth0 e eth1 interfacelerindeki mac adreslerinin bulunması, hangi interface’in down olduğunu anlamanız açısından faydalı olacaktır.
{{% /notice %}}

## Fault Tolerant ( Aktif/Pasif Mod )

Ben bond çalışma modunu aktif/pasif moda göre düzenleyeceğim. Diğer mod ayarlarını [linkten](https://www.centos.org/docs/5/html/5.1/Deployment_Guide/s3-modules-bonding-directives.html) bulabilirsiniz.

```sh
# vi /etc/modprobe.d/bonding.conf
```

```sh
alias bond0 bonding
    options bond0 miimon=80 mode=1
```

{{% notice info %}}
* bond0 = Oluşturduğumuz bond interface ismi.
* miimon = Ne kadar milisaniyede interface durumunu kontrol etsin.
* mode = Bond interface’in çalışma prensibi
{{% /notice %}}

Yukardaki şekilde düzenlemelerimizi yaptıktan sonra network servisini  yeniden başlatıyoruz.

```sh
# service network restart
```

```sh
# ifconfig
bond0     Link encap:Ethernet
          inet addr:172.16.41.10 Bcast:172.16.41.255  Mask:255.255.252.0
          UP BROADCAST RUNNING MASTER MULTICAST  MTU:1500  Metric:1
 
eth0      Link encap:Ethernet  
          inet addr:172.16.41.10  Bcast:172.16.41.255  Mask:255.255.252.0
          UP BROADCAST RUNNING SLAVE MULTICAST  MTU:1500  Metric:1
 
eth1      Link encap:Ethernet  
          inet addr:172.16.41.10  Bcast:172.16.41.255  Mask:255.255.252.0
          UP BROADCAST RUNNING SLAVE MULTICAST  MTU:1500  Metric:1
```