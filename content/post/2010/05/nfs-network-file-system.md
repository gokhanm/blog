---
title: "Nfs - Network File System"
date: 2010-05-06T11:24:47+03:00
url: 2010/05/nfs-network-file-system
---

NFS dosya sistemi kullanıcılardan bağımsız olarak ağdaki makinaların ağdaki başka bir makinanın hdd sini kendi hdd si gibi kullanması mantığı ile çalışır.

Makinenizdeki bir dizini ağdaki diğer makineler ile paylaşmak için

```sh
/etc/exports
```

dosyasına hangi dizini hangi makinelerin kullanacağını ve hangi hak ile üzerinde değişiklik yapılacağını belirtiyorsunuz.

```sh
Paylaşım Dizini                         Makine Adı(Seçenekler)

/home/Gokhan                            (ro)
/home/Ahmet                             *.gokhanmankara.com  (ro)
/home/Veli                              *.gokhanmankara.com(rw)
```

Burada 3 tane farklı dizini paylaştırmış oldum.

**”/home/Gokhan”** dizinini ağdaki tüm makinalara read-only hakkı ile paylaştırdık.

**“/home/Ahmet”**   dizinini **“*.gokhanmankara.com”** alanı ve dışında olanlara read-only hakkı ile paylaştırdık.

**“/home/Veli”** dizinini **”*.gokhanmankara.com”** alanındakilere read-write hakkı ile paylaştırmış olduk. Diğer makineler yazma ve okuma yapamaz.

Burada dikkat etmemiz gereken (ro) ve (rw) lerin ayrı ve bitişik yazılma durumlarında ne anlama geldikleri.

exports dosyasını bu şekilde düzenledikten sonra geldi diğer makinelerda bu dizinleri mount etmeye.

Mesela dosyaları paylaşacağımız makinenin ipsi 192.168.0.10 olsun

```sh
$ mount -t nfs 192.168.0.10:/home/Gokhan   /mnt
```

Burada yaptığımız paylaşıma açtığımız dosyayı /mnt dizinine bağlamış olduk.