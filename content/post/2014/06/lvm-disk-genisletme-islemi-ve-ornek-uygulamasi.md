---
title: "LVM disk genişletme işlemi ve örnek uygulaması"
date: 2014-06-04T15:26:39+03:00
categories:
  - nix
  - notlar
tags:
  - disk genişletme
  - esxi
  - Logical Volume
  - LVM
  - örnek uygulama
  - Physical Volume
  - sanal sunucu
  - Volume Group
url: /2014/06/lvm-disk-genisletme-islemi-ve-ornek-uygulamasi
---
[LVM](http://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)) (Logical Volume Manager), mantıksal hacim yönetimi olarak çevirebileceğimiz, sistem üzerinde disk kümesi yada kümeleri oluşturmaya olanak sağlayan bir disk yönetim sistemidir. LVM kullanarak mevcut disk sistemi üzerinde online olarak yeniden boyutlandırmaya olanak sağlamaktadır.

Eğer sisteminiz default ayarlar ile kurulum yapıldıysa, disk yapınız büyük olasılıkla LVM ile yapılandırılmıştır. Bir çok dağıtım default olarak LVM disk yapısını kullanmaktadır.

LVM olarak yapılandırılmış bir sunucuda disk alanını genişletmek için nasıl bir yöntem izlenmesi gerektiğine bakacağız. 16 Gb disk üzerine kurulan bir sanal sunucunun root ( / ) dizininin kapasitesini arttırarak toplamda 50 Gb  lık bir disk alanını online olarak genişletme işlemi yapacağız.

```sh
[root@test ~]# fdisk -l
 
Disk /dev/sda: 17.2 GB, 17179869184 bytes
255 heads, 63 sectors/track, 2088 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00087c1e
 
   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *           1          64      512000   83  Linux
Partition 1 does not end on cylinder boundary.
/dev/sda2              64        2089    16264192   8e  Linux LVM
 
Disk /dev/mapper/vg_test-lv_root: 14.9 GB, 14935916544 bytes
255 heads, 63 sectors/track, 1815 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000
 
 
Disk /dev/mapper/vg_test-lv_swap: 1715 MB, 1715470336 bytes
255 heads, 63 sectors/track, 208 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000
```

Sunucumuz 16 Gb lık bir diske default LVM ile kurulum yapılmış. Bu disk kapasitesini 50 Gb a arttırıp, yaklaşık 30 Gb lık boş alanı root ( test-lv_root ) partitiona ekleyeceğiz.

Disk kapasite arttırma işlemini kullandığınız ESXi, XenServer yada fiziksel bir sunucuya göre yapabilirsiniz. Bu makalede ESXi 5.x de bir sanal sunucunun disk kapasitesini 16 gb dan 50 gb a aldığımızı düşünelim.


> Sanal sunucuyu yeniden başlatmadan, düzenlediğiniz yeni disk boyutunu sanal sunucuda görebilmek için aşağıdaki kodu çalıştırabilirsiniz.
>```sh
> echo 1 > /sys/block/sda/device/rescan
```


Toplam boyutu ayarladıktan sonra yeni disk boyutuna bakalım.

```sh
[root@test ~]# fdisk -l
 
Disk /dev/sda: 53.7 GB, 53687091200 bytes
255 heads, 63 sectors/track, 6527 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00087c1e
 
   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *           1          64      512000   83  Linux
Partition 1 does not end on cylinder boundary.
/dev/sda2              64        2089    16264192   8e  Linux LVM
 
Disk /dev/mapper/vg_test-lv_root: 14.9 GB, 14935916544 bytes
255 heads, 63 sectors/track, 1815 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000
 
 
Disk /dev/mapper/vg_test-lv_swap: 1715 MB, 1715470336 bytes
255 heads, 63 sectors/track, 208 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000
```

Yeni disk boyutumuz 50 Gb olarak görünüyor. Yeni düzenlemeden sonra yaklaşık olarak 34 Gb lık bir alan boşta duruyor. Bunun için yapmamız gereken işlem sırasıyla, önce yeni bir disk bölümü (partition) oluşturacağız  sonra bu disk bölümü için yeni bir fiziksel volüm oluşturacağız ve bu fiziksel bölümü volume group’a dahil etmeceğiz. Sonrasında da var olan LVM yapısını yeni disk bölümünü kapsayacak şekilde genişleteceğiz.

{{% notice note %}}
* Volume Group (Hacim Gurubu): En üstte bulunan bu katman, fiziksel ve mantıksal volüm’leri içine almaktadır. Yani fiziksel disklerin bulunduğu gruptur.
* Physical Volume (Fiziksel Hacim): PV ise fiziksel aygıtlardan (disk) ve disk bölümlerinden oluşan kısımdır.
* Logical Volume (Mantıksal Hacim): LV, disk ve ya disk bölümlerinin formatlanmasından sonra ortaya çıkan dosya sistemi içeren ( ext3 ile formatlanmış kök dizini  / ) yapıya verilen isimdir.
{{% /notice %}}

Fdisk ile 8e dosya sisteminde ( LVM ) bir partition oluşturuyoruz.

```sh
#fdisk /dev/sda

n (yeni)
p (primary)
3 (sda1 ve sda2 mevcut, bu yüzden sda3 oluşturacağız.)
t (partition sistem id yi LVM (8e) olarak değiştireceğiz)
3 (sda3 partition numarası)
8e ( LVM )
p (partition oluşturduktan sonra işlemi yazdırmadan kontrol ediyoruz.)
w (yeni partition yazılır/kaydedilir.)
```

Yeni sda3 partition oluşturduktan sonra yeni fdisk çıktımıza bakalım,

```sh
[root@test ~]# fdisk -l
 
Disk /dev/sda: 53.7 GB, 53687091200 bytes
255 heads, 63 sectors/track, 6527 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00087c1e
 
   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *           1          64      512000   83  Linux
Partition 1 does not end on cylinder boundary.
/dev/sda2              64        2089    16264192   8e  Linux LVM
/dev/sda3            2089        6527    35650911+  8e  Linux LVM
 
Disk /dev/mapper/vg_test-lv_root: 14.9 GB, 14935916544 bytes
255 heads, 63 sectors/track, 1815 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000
 
 
Disk /dev/mapper/vg_test-lv_swap: 1715 MB, 1715470336 bytes
255 heads, 63 sectors/track, 208 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000
```

Fdisk çıktımızda yeni oluşturduğumuz sda3 LVM partitionu görünüyor.  Şimdi sırada oluşturduğumuz yeni sda3 partitionı fiziksel volüme dönüştüreceğiz.

>Bundan önce sistemin partition sda3 ü tanıması için restart etmeniz gerekiyor yada aşağıdaki kodu çalıştırmalısınız.
>
>```sh
$ partx -v -a /dev/sda

device /dev/sda: start 0 size 104857600
gpt: 0 slices
dos: 4 slices
  1: 2048- 1026047 ( 1024000 sectors, 524 MB)
  2: 1026048- 33554431 ( 32528384 sectors, 16654 MB)
  3: 33554432-104856254 ( 71301823 sectors, 36506 MB)
  4: 0- -1 ( 0 sectors, 0 MB)
BLKPG: Device or resource busy
error adding partition 1
BLKPG: Device or resource busy
error adding partition 2
added partition 3
```

**added partition 3** artık yeni partition sistem tarafından tanınmış oldu. Artık fiziksel volüme olarak ekleyebiliriz.

```sh
[root@test ~]# pvcreate /dev/sda3 
  dev_is_mpath: failed to get device for 8:3
  Physical volume "/dev/sda3" successfully created
```

Oluşturduğumuz yeni fiziksel volümü, boş alan olarak volume group’a ekliyoruz.

>Test sunucusundaki volume group “vg_test” olarak adlandırılmış. Sizin sisteminizde volume group isminin ne olduğunu bilemiyor iseniz aşağıdaki komut ile volume group ismini öğrenebilirsiniz.
>```sh
[root@rbl ~]# vgdisplay
  --- Volume group ---
  VG Name               vg_test
  System ID             
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  3
.....
  Total PE              3970
  Alloc PE / Size 3970 / 15.51 GiB
  Free PE / Size 0 / 0
```

```sh
[root@test ~]# vgextend vg_test /dev/sda3
  Volume group "vg_test" successfully extended
```

Sda3 partition alanını vg_test isimli volume group grubuna ekledik. Dikkat etti iseniz gruba eklemeden önceki vgdisplay komutunda Free PE / Size sıfır olarak görünüyor. Yani genişletme işlemi için boş disk alanımız bulunmuyor. Sda3 partitionı vg_test grubuna ekledikten sonraki vgdisplay çıktısına bakalım.

```sh
[root@rbl ~]# vgdisplay
  --- Volume group ---
  VG Name               vg_test
  System ID             
  Format                lvm2
  Metadata Areas        2
  Metadata Sequence No  4
.....
  Total PE              12673
  Alloc PE / Size       3970 / 15.51 GiB
  Free  PE / Size       8703 / 34.00 GiB
```
Yeni vgdisplay çıktısında Free alan olarak 34 Gb boş alan görünüyor. Bu yeni boş alanı 14 gb lık root dizinini genişletmek için kullanacağız.

```sh
[root@test ~]# df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/mapper/vg_test-lv_root
                       14G  1.3G   12G  10% /
tmpfs                 939M     0  939M   0% /dev/shm
/dev/sda1             485M   67M  393M  15% /boot
```

```sh
[root@test ~]# lvextend -l +100%FREE /dev/mapper/vg_test-lv_root 
  Extending logical volume lv_root to 47.91 GiB
  Logical volume lv_root successfully resized
```

lvextend komutu ile boş  tüm ( +100%FREE ) disk alanını root dizinine ekleyerek genişlettik. Son durumdaki vgdisplay boş alanına bakalım.

```sh
[root@test ~]# vgdisplay 
  --- Volume group ---
  VG Name               vg_test
  System ID             
  Format                lvm2
  Metadata Areas        2
  Metadata Sequence No  5
----
 Total PE 12673
 Alloc PE / Size 12673 / 49.50 GiB
 Free PE / Size 0 / 0
```

lvextend komutunda tüm boş alanı kullan dediğimiz için hiç boş ( free ) disk alanı kalmadı.

Tekrar disk kullanım alanına bakacak olursak,

```sh
[root@rbl ~]# df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/mapper/vg_test-lv_root
                       14G  1.3G   12G  10% /
tmpfs                 939M     0  939M   0% /dev/shm
/dev/sda1             485M   67M  393M  15% /boot
```

Root dizini hala 14 gb olarak görünüyor. Bunun sebebi root dizinini resize etmediğimizden dolayıdır. Online resize işlemi için aşağıki komutu kullanıyoruz.

```sh
[root@test ~]# resize2fs -p /dev/mapper/vg_test-lv_root
 
resize2fs 1.41.12 (17-May-2010)
Filesystem at /dev/mapper/vg_test-lv_root is mounted on /; on-line resizing required
old desc_blocks = 1, new_desc_blocks = 3
Performing an on-line resize of /dev/mapper/vg_test-lv_root to 12558336 (4k) blocks.
The filesystem on /dev/mapper/vg_test-lv_root is now 12558336 blocks long.
```

Tekrar disk kullanımına baktığımızda root dizininin toplam boyutunun 48 Gb olduğu görülüyor

```sh
[root@rbl ~]# df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/mapper/vg_test-lv_root
                       48G  1.3G   44G   3% /
tmpfs                 939M     0  939M   0% /dev/shm
/dev/sda1             485M   67M  393M  15% /boot
```

