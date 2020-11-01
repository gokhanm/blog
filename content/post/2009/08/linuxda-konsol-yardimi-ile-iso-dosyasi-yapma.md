---
title: "Linuxda Konsol Yardımı İle Iso Dosyası Yapma"
date: 2009-08-26T12:01:47+03:00
url: 2009/08/linuxda-konsol-yardimi-ile-iso-dosyasi-yapma
categories:
  - nix
tags:
  - iso
  - iso dosyası oluşturma
  - konsol üzerinden iso oluşturma
---
Virtual PC yada Vmware kullanıyorsanız hızlı bir kurulum yapmak için en iyi yol imaj kullanmaktır. Windows da imaj yapmak için yardımcı programlar kullanmak gerekir. Linux’da ise bu işlemi çok kısa bir sürede konsol yardımıyla yapmak mümkün.

Bunun için bir klasör oluşturun ve dosyaları onun içine kopyalayın. Daha sonra oluşturduğunuz klasörün imajını almak için kısa bir komut yazmak yeterli olacaktır.

```sh
# mkisofs -R -J -o deneme.iso /home/Gokhan/Documents/deneme
```

```sh
I: -input-charset not specified, using utf-8 (detected in locale settings)
Using HITCH000.MP3;1 for /home/Gokhan/Documents/deneme/Hitch 1 Lessons/Hitch 1 Lessons/Hitch 1 Commentary.mp3 (Hitch 1 MS.mp3)
Using HITCH001.MP3;1 for /home/Gokhan/Documents/deneme/Hitch 1 Lessons/Hitch 1 Lessons/Hitch 1 MS.mp3 (Hitch 1 MS-POV.mp3)
Using HITCH002.MP3;1 for /home/Gokhan/Documents/deneme/Hitch 1 Lessons/Hitch 1 Lessons/Hitch 1 MS-POV.mp3 (Hitch 1 Vocab.mp3)
18.33% done, estimate finish Wed Aug 26 22:04:46 2009
36.67% done, estimate finish Wed Aug 26 22:04:46 2009
54.97% done, estimate finish Wed Aug 26 22:04:46 2009
73.25% done, estimate finish Wed Aug 26 22:04:46 2009
91.56% done, estimate finish Wed Aug 26 22:04:46 2009
Total translation table size: 0
Total rockridge attributes bytes: 1850
Total directory bytes: 8548
Path table size(bytes): 72
Max brk space used 21000
27312 extents written (53 MB)
```

Sonuç olarak deneme klasörünün içine **deneme.iso** adında bir dosya yaratmış olduk.

