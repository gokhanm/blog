---
title: "Küçük Notlar - Ssh Ile Çift Tire Kullanımı"
date: 2014-06-01T11:01:34+03:00
url: 2014/06/kucuk-notlar-ssh-ile-cift-tire-kullanimi
categories:
  - notlar
tags:
  - çift tire
  - double dash
  - küçük notlar
  - ssh
  - ssh ile çift tire kullanımı
---

SSH üzerinde çalışırken, başka bir uzak sunucuda,  sunucuya dosya indirmek yada bir klasörün içeriğini görmek için ssh komutundan sonra çift tire ( double dash ) koyarak, sunucuya ssh bağlantısı ile giriş yapmadan uzak sunucuda  komut çalıştırabilirsiniz.

# Küçük Notlar - Ssh Ile Çift Tire Kullanımı

SSH üzerinde çalışırken, başka bir uzak sunucuda,  sunucuya dosya indirmek yada bir klasörün içeriğini görmek için ssh komutundan sonra çift tire ( double dash ) koyarak, sunucuya ssh bağlantısı ile giriş yapmadan uzak sunucuda  komut çalıştırabilirsiniz.

```sh
ssh root@sunucu -- command1 --arg1 --arg2
```

```sh
$ ssh root@192.168.1.100 -- ls -alh /root/
root@192.168.1.100's password:
```

```sh
total 68K
dr-xr-x---. 2 root root 4.0K May 30 15:41 .
dr-xr-xr-x. 21 root root 4.0K Jun 1 20:26 ..
-rw-------. 1 root root 1.3K May 30 15:19 anaconda-ks.cfg
-rw-------. 1 root root 316 Jun 1 20:31 .bash_history
-rw-r--r--. 1 root root 18 May 20 2009 .bash_logout
-rw-r--r--. 1 root root 176 May 20 2009 .bash_profile
-rw-r--r--. 1 root root 176 Sep 23 2004 .bashrc
-rw-r--r--. 1 root root 100 Sep 23 2004 .cshrc
-rw-r--r--. 1 root root 17K May 30 15:19 install.log
-rw-r--r--. 1 root root 5.8K May 30 15:17 install.log.syslog
$
```