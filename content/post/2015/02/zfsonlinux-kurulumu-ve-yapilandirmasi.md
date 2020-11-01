---
title: "Zfsonlinux Kurulumu Ve Yapılandırması"
date: 2015-02-10T10:45:52+03:00
categories:
  - nix
  - LKD-Gezegen
tags:
  - zfs
  - zfs kurulumu
  - zfs pool
  - zfs yapılandırması
  - zfsonlinux
  - zpool
url: /2015/02/zfsonlinux-kurulumu-ve-yapilandirmasi
---
[Zfs](http://en.wikipedia.org/wiki/ZFS), yüksek ölçeklenebilirliğe uyumlu, aşağıdaki kriterleri dikkate alarak dizayn edilmiş dosya sistemi ve mantıksal hacim yöneticidir.

* Veri bütünlüğü
* Basit depolama yönetimi
* Her şeyi online olarak yapabilmesi

Bsd sistemlerde default olarak gelen zfs dosya sistemini, zfsonlinux kurarak linux sistemlerde de kullanma imkanı bulabiliyoruz.

## Zfsonlinux Kurulumu

Redhat tabanlı işletim sistemi için zfsonlinux [download](http://zfsonlinux.org/epel.html) sayfasından 6 ve 7 dağıtımları için gerekli rpm repolarını indirebilirsiniz. Bu yazıda Centos 6 için kurulum yapılacaktır.

{{% notice warning %}}
Zfsonlinux, 64bit kernel üzerinde kullanmanızı öneriyor. 32bit kernel üzerinde henuz stabil olarak çalışmadığı için önerilmiyor.
{{% /notice %}}

```sh
# rpm -ivh https://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
# rpm -ivh http://archive.zfsonlinux.org/epel/zfs-release.el6.noarch.rpm
# yum install kernel-devel zfs
```

{{% notice info %}}
Oracle Linux kullanıyor iseniz, paket kurulumları ardından kernel üzerinde gerekli düzenlemenin yapılabilmesi için sunucuyu yeniden başlatmak gerekiyor. Sistemin boot olduğu sırada işlem gerçekleştiği için sunucunun açılışı uzun sürmekte. Sanal test ortamında, Centos 6 da derlenme işlemi yapmadan hızlı şekilde açılıyor.
{{% /notice %}}

```sh
# reboot
```

Sunucuyu yeniden başlattıktan sonra aşağıdaki şekilde hata almanızın sebebi  kernel, kernel-headers ve kernel-devel versiyonlarının uyuşmamasından kaynaklanıyor olabilir. Kernel’i güncellemeniz problemi çözecektir.

```sh
# zfs list
Failed to load ZFS module stack.
Load the module manually by running 'insmod <location>/zfs.ko' as root.
 
[root@localhost ~]# rpm -qa |grep kernel
kernel-firmware-2.6.32-504.el6.noarch
dracut-kernel-004-356.el6.noarch
kernel-2.6.32-504.el6.x86_64
kernel-devel-2.6.32-504.8.1.el6.x86_64
kernel-headers-2.6.32-504.8.1.el6.x86_64
```

## Zfs Pool Oluşturulması

Zfs kurulumu yapılan sunucuda 2 disk mevcut, __2. disk zfs olarak yapılandırılıp__, tank0 olarak zfs pool oluşturacağız. 2.diski sisteme mount etmediğimden dolayı “df ” çıktısında görünmüyor. Eğer önceden ext4 olarak formatlayıp mount ettiğiniz bir diski kullancak iseniz ilk önce umount edip, “/etc/fstab” dosyasından diskin mount edildiği satırın başına “#” koymanız yada satırı silmeniz gerekiyor. Bu sayede zfs diski legacy olarak fstab üzerinden sisteme sorunsuz mount edebiliriz.

```sh
# zpool create tank0 /dev/sdb 
# zfs list 
NAME    USED  AVAIL  REFER  MOUNTPOINT
tank0   108K  19.6G    30K  /tank0
```

{{% notice info %}}
zpool create sırasında aşağıdaki şekilde EFI label hata alırsanız “-f ” parametresi ile komutu tekrar çalıştırın.
invalid vdev specificationuse ‘-f’ to override the following errors:
/dev/sdb does not contain an EFI label but it may contain partitioninformation in the MBR.
{{% /notice %}}

tank0 poolunun yanında aynı disk üzerinde birden fazla poollar da oluşturabilirsiniz.

```sh
# zfs create -o mountpoint=/home/zfs1 tank0/zfs1
 
# zfs list
NAME         USED  AVAIL  REFER  MOUNTPOINT
tank0        156K  19.6G    30K  /tank0
tank0/zfs1    30K  19.6G    30K  /home/zfs1
 
# df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/mapper/VolGroup-lv_root
                       11G  961M  8.6G  10% /
tmpfs                 499M     0  499M   0% /dev/shm
/dev/sda1             477M   49M  403M  11% /boot
tank0                  20G  128K   20G   1% /tank0
tank0/zfs1             20G  128K   20G   1% /home/zfs1
```

Poolları legacy olarak oluşturmadığımız için sunucuyu resetlemeniz durumunda zfs poolları sunucunun açılışında oluşturulamayacak ve ” df ” çıktısında görünmeyecektir.

```sh
# zfs list
NAME         USED  AVAIL  REFER  MOUNTPOINT
tank0        158K  19.6G    30K  /tank0
tank0/zfs1    30K  19.6G    30K  /home/zfs1
 
# df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/mapper/VolGroup-lv_root
                       11G  961M  8.6G  10% /
tmpfs                 499M     0  499M   0% /dev/shm
/dev/sda1             477M   49M  403M  11% /boot
```

Bu sebeble aşağıdaki şekilde zfs poollarını mointpoint olarak legacy ayarlayıp, sunucunun açılışı sırasında fstab ayarlarına göre mount olmasını sağlıyoruz.

```sh
# zfs set mountpoint=legacy tank0
 
# zfs get mountpoint tank0
NAME   PROPERTY    VALUE       SOURCE
tank0  mountpoint  legacy      local
 
# zfs set mountpoint=legacy tank0/zfs1
 
# zfs get mountpoint tank0/zfs1
NAME        PROPERTY    VALUE       SOURCE
tank0/zfs1  mountpoint  legacy      local
```

fstab dosyasına aşağıdaki satırları ekliyoruz.

```sh
tank0                   /tank0                  zfs     defaults        0 0
tank0/zfs1              /home/zfs1              zfs     defaults        0 0
```

Artık poolları mount edebiliriz.

```sh
# mount -a
 
# df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/mapper/VolGroup-lv_root
                       11G  961M  8.6G  10% /
tmpfs                 499M     0  499M   0% /dev/shm
/dev/sda1             477M   49M  403M  11% /boot
tank0/zfs1             20G     0   20G   0% /home/zfs1
tank0                  20G     0   20G   0% /tank0
```

Zfs komutunun ayrıntılarını [linkte](https://www.freebsd.org/cgi/man.cgi?zfs%288%29) bulabilirsiniz.

## Performans Ayarları

Sunucularda performans amacı ile kullandığım birkaç zfs ayarlarından bahsedecek olursak,

* Çok fazla i/o yapan bir sunucuda zfs compression özelliğini aktif ederek i/o sorununu azaltabilirsiniz.

```sh
# zfs get compression
NAME        PROPERTY     VALUE     SOURCE
tank0       compression  off       default
tank0/zfs1  compression  off       default
 
# zfs set compression=on tank0
# zfs set compression=on tank0/zfs1
 
# zfs get compression
NAME        PROPERTY     VALUE     SOURCE
tank0       compression  on        local
tank0/zfs1  compression  on        local
```

* recordsize değerini değiştirebilirsiniz.

```sh
# zfs set recordsize=128K tank0
# zfs set recordsize=128K tank0/zfs1
 
# zfs get recordsize
NAME        PROPERTY    VALUE    SOURCE
tank0       recordsize  128K     default
tank0/zfs1  recordsize  128K     default
```