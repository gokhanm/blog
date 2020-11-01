---
title: "Centos Ramdisk Oluşturma"
date: 2014-02-25T12:13:27+03:00
url: 2014/02/centos-ramdisk-olusturma
categories:
  - nix
tags:
  - centos
  - mysql
  - performans
  - ramdisk oluşturma
  - sunucu
  - tmpfs
---
Sunucularda ramdisk kullanım amacı, genel olarak yoğun disk kullanıma neden olan sistemlerde, sunucunun resetlenmesinde verilerin kaybolmasının önemli olmadığı dosyaların ( tmp dizinine yazılan mysql dosyaları vb. ) ram alanında tutularak sunucunun performansını arttırmak içindir.

Bir sunucunun en yavaş donanımı harddisktir. Özellikle yoğun kullanılan mysql sorgularında mysql, tmp dizinine yoğun şekilde okuma yazma yaptığından harddisk buna yetişemez ve sorguların birikmesine, sunucu loadlarının yükselmesine, swap alanının dolmasına, bir web sitesi barındırılıyor ise sitenin geç açılmasına varan sebeblerin önüne geçmek için mysql tmp dosyası ramdiskte tutulur. Bu sayede mysql sorguları birikmez ve daha hızlı sql sorgularına cevap verebilir.

## Ram Disk Yapılandırması

Centos 6 üzerinden 512 Mb lık ramdisk nasıl yapıldığını bakacak olursak,

İlk önce grub.conf dosyasında bulunan kernel satırının en sonuna **“ramdisk_size=524288” ( KByte cinsinden değer )** eklememiz gerekiyor. Bu işlemden sonra sunucuyu resetlememiz gerekir ki grub.conf dosyasında yapmış olduğumuz değişiklik algılansın.

```sh
title CentOS (2.6.32-431.5.1.el6.x86_64)
	root (hd0,0)
	kernel /vmlinuz-2.6.32-431.5.1.el6.x86_64 ro root=/dev/mapper/vg_test-lv_root rd_NO_LUKS  KEYBOARDTYPE=pc KEYTABLE=trq LANG=en_US.UTF-8 rd_NO_MD rd_LVM_LV=vg_test/lv_swap SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_NO_DM rd_LVM_LV=vg_test/lv_root rhgb quiet ramdisk_size=524288
	initrd /initramfs-2.6.32-431.5.1.el6.x86_64.img
```

Bu satırı eklemememiz durumunda mkfs komutu sırasında aşağıdaki şekilde hata almanız yada işlem sonrası test etmek amacı ile sunucunun resetlenmesi sonrası ramdisk oluşmaması mümkün.

```sh
mkfs.ext2: Filesystem larger than apparent device size.
```

Sunucu resetledikten sonra aşağıdaki işlemleri gerçekleştiriyoruz,

```sh
# mkfs -t ext2 -m 0 -q /dev/ram1 524288
# mkdir -p /ramdisk
# mount /dev/ram1 /ramdisk
# chmod 777 /ramdisk
# df -H | grep ramdisk
```

```sh
Filesystem                   Size  Used Avail Use% Mounted on
/dev/ram1                    529M  406k  529M   1% /ramdisk
```

> * Herhangi bir veri güvenliği ön planda olmadığı için ext2 dosya sistemini kullandık.
> * -m parametresi ile %5 lik kısmı rezerve etme, hepsini ben kullanacağım diyoruz.
> * -q parametresi ile herhangi bir çıktı üretmemesini sağlıyoruz.
> * ramdisk dizinine 777 iznini vermemizin sebebi, mysql’in okuma, yazma ve çalıştırma izni istemesi

Ram üzerinde 512 Mb bir alan ayırarak ramdisk dizinine bağladık, bu  sayede mysql conf ( my.cnf ) dosyasında geçen tmp satırını **“/ramdisk”** olarak düzenlediğimizde artık mysql tmp dizini olarak ram üzerinde ayırdığımız alanı kullanacaktır. Bu sayede disk üzerinde i/o yükünü azaltarak, sunucudan daha iyi bir performans alabiliriz.

## İnit Scripti

Eğer sunucuyu yeniden başlatırsak ne olacak? Yaptığımız ayarlar sıfırlanacaktır. Ayarların gitmemesi için bir init.d scripti oluşturarak, sunucunun yeniden başlaması sırasında bu script çalıştırıp, ramdisk ayarlarını tekrar yapılandırılmasını sağlayacağız.

```sh
# vi /etc/init.d/ramdisk.sh
```
ramdisk.sh dosyasına aşağıdaki scripti yazıyoruz.

```sh
#!/bin/sh
mkfs -t ext2 -m 0 -q /dev/ram1 524288
[ ! -d /ramdisk ] && mkdir -p /ramdisk
mount /dev/ram1 /ramdisk
chmod 777 /ramdisk
```
Daha sonra ramdisk.sh dosyasına çalıştırma izni verip, rc.local dosyasına sunucunun boot sırasında scriptin çalışması için ekliyoruz.

```sh
# chmod +x /etc/init.d/ramdisk.sh
# echo '/etc/init.d/ramdisk.sh' >> /etc/rc.local
```

> Ramdisk, [tmpfs](https://www.kernel.org/doc/Documentation/filesystems/tmpfs.txt) ile karıştırılmaması gerekir. 
> Linux sistemlerde tmpfs adı ile /dev/shm dizinine mount edilmiştir. 
> Tmfs ram (memory) üzerinden dinamik olarak yer kaplar ve az kullanılan sayfaların swap alanına taşır. 
> Oysa bizim burada yaptığımız işlem ram ( memory ) alanından bir partition gibi alan ayırarak dizine mount etme işlemidir. 
> Tmpfs gibi swapla herhangi bir ilgisi yoktur.