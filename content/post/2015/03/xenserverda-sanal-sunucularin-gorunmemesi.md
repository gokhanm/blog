---
title: "Xenserver'da Sanal Sunucuların Görünmemesi"
date: 2015-03-31T13:54:53+03:00
categories:
  - Sanallastirma
tags:
  - host
  - hostların görünmemesi
  - xen
  - xenserver
url: /2015/03/xenserverda-sanal-sunucularin-gorunmemesi
---
XenServer host sunucudaki elektrik kesintisi yada benzer fiziksel sorunlar sebebiyle, storage üzerindeki sanal makineler kapanmadan bilgisayar resetlenebilir, kapanabilir veya fiziksel arıza yüzüden hiç açılmayabilir. Bu durumda ilgili host bilgisayar kapanmadan önce
üzerinde çalışan sanal makineler Xencenter arayüzünde kaybolur. Haliyle sanal makineyi başka bir host bilgisayara taşımak da mümkün olmaz. Peki bu durumdan nasıl kurtulunur.

Pool üzerindeki bir host bilgisayarın konsoluna bağlandıktan sonra **“xe host-list”** komutu işletilir. Havuzdaki host makineler listelenir. Düşen host makine tespit edilip id, sonraki komutta kullanılmak üzere alınır.

```sh
# xe vm-list resident-on=#Host bilgisayar ID# --multiple
```
Komutuyla , bu kez host bilgisayar üzerinde kaybolan sanal makinelerin id leri alınır.

```sh
# xe vm-reset-powerstate vm=#Sanal Makine ID# force=true
```

Komutu ile sanal makinelerin power durumları resetlenir. Sanal makineler xencenter’da görünmeye başlar.

Görünen sanal makineler  istenilen host üzerinde başlatılmaya çalışılır, eğer **“VDI Not Available”** hatası alınıp sanal makineler başlamazlarsa **“xe vdi-list komutu”** ile tüm sanal makinelerin listesi alınır. Eğer çok sanal makine ve diskler varsa  
```sh
xe vdi-list > /home/liste.txt
```
liste incelenmek üzere export edilmesi faydalı olur.

Power state leri değiştiği andan itibaren xencenter’da görülmeye başlayan sorunlu sanal makinelerin idleri ile ilişkilendirilmiş vdi(sanal disk) ler liste üzerinden bulunur, idleri alınır.
Sorunlu sanal makine üzerinde birden fazla disk bağlanmış ise her vdi için aşağıdaki komut işletilir.

```sh
# /opt/xensource/sm/resetvdis.py single #Vdi ID# --force
```

Ve sanal makineler başlatılır.


Hazırlayan: [Şemsettin AKSOY](https://tr.linkedin.com/in/semsaksoy)

