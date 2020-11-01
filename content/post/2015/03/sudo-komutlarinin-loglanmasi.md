---
title: "Sudo Komutlarının Loglanması"
date: 2015-03-22T14:15:19+03:00
categories:
  - notlar
  - LKD-Gezegen
tags:
  - sudo
  - sudo komutlarının loglanması
  - sudo log
  - sudoers
url: /2015/03/sudo-komutlarinin-loglanmasi
---
Daha önceki [yazımda](/2014/05/sudoers-listesine-kullanici-ekleme/) kullanıcıya nasıl sudo yetkisi verilebileceğinden bahsetmiştim. Bu yazıda da sudo yetkisi verdiğiniz kullanıcının uyguladığı komutları nasıl loglayacağımıza bakacağız. Önceki yazıda sudoers dosyasına kullanıcıyı eklemiştik, bu yazıda sudoers.d altına kullanıcıları oluşturacağız.

```sh
# vi /etc/sudoers.d/gokhan
```

```sh
gokhan   ALL=(ALL)   NOPASSWD: LOG_INPUT: LOG_OUTPUT: ALL
Defaults iolog_dir=/var/log/sudo-io/%{user}
```

gokhan kullanıcısına şifresiz olarak sudo komutu çalıştırma yetkisi vermiş olduk. iolog_dir dizinini oluşturmamıza gerek yok, default olarak dizin oluşacaktır.

sudo-io dizinine baktığınızda birden fazla dosya oluştuğunu görürsünüz.

```sh
└── gokhan
    ├── 00
    │   └── 00
    │       ├── 01
    │       │   ├── log
    │       │   ├── stderr
    │       │   ├── stdin
    │       │   ├── stdout
    │       │   ├── timing
    │       │   ├── ttyin
    │       │   └── ttyout
    │       ├── 02
    │       │   ├── log
    │       │   ├── stderr
    │       │   ├── stdin
    │       │   ├── stdout
    │       │   ├── timing
    │       │   ├── ttyin
    │       │   └── ttyout
    │       ├── 03
    │       │   ├── log
    │       │   ├── stderr
    │       │   ├── stdin
    │       │   ├── stdout
    │       │   ├── timing
    │       │   ├── ttyin
    │       │   └── ttyout
    │       ├── 04
    │       │   ├── log
    │       │   ├── stderr
    │       │   ├── stdin
    │       │   ├── stdout
    │       │   ├── timing
    │       │   ├── ttyin
    │       │   └── ttyout
    │       ├── 05
    │       │   ├── log
    │       │   ├── stderr
    │       │   ├── stdin
    │       │   ├── stdout
    │       │   ├── timing
    │       │   ├── ttyin
    │       │   └── ttyout
    │       ├── 06
    │       │   ├── log
    │       │   ├── stderr
    │       │   ├── stdin
    │       │   ├── stdout
    │       │   ├── timing
    │       │   ├── ttyin
    │       │   └── ttyout
    │       └── 07
    │           ├── log
    │           ├── stderr
    │           ├── stdin
    │           ├── stdout
    │           ├── timing
    │           ├── ttyin
    │           └── ttyout
    └── seq
```

Bu dosya çokluğunun içinde kaybolmak yerine aşağıdaki komut ile sudo izni verdiğiniz kullanıcının neler yaptığını görebilirsiniz.

```sh
# find /var/log/sudo-io/ -name log -exec cat {} \; | grep "::" -A 2 | awk -F: '$1 ~ /^[0-9]+$/{printf  "%s%s",(NR>1)?"\n":"",$1;next} {printf ";" $0} END{printf "\n"}'
```

```sh
1427012536;/var/log;/usr/bin/yum install
1427014071;/var/log;/bin/cat /etc/passwd
1427011890;/home/gokhan;/usr/bin/yum update
1427014063;/var/log;/bin/cat /etc/shadow
1427010846;/home/gokhan;/bin/ls /var/log/sudo-io/
1427010852;/home/gokhan;/bin/ls /var/log/sudo-io/gokhan
1427010823;/home/gokhan;/usr/bin/yum update
1427010863;/home/gokhan;/bin/cat /var/log/sudo-io/gokhan
1427011064;/home/gokhan;/usr/bin/yum update
```