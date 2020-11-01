---
title: "Tmpwatch İle Tmp Klasörünü Temizleme"
date: 2010-07-22T15:03:11+03:00
url: 2010/07/tmpwatch-ile-tmp-klasorunu-temizleme
categories:
  - nix
tags:
  - gereksiz
  - tmp
  - tmpwatch
---
Bugün /tmp klasörünün şişmesinden nasıl kurtulurum diye araştırma yaparken Red Hat tarafından geliştirilen tmpwatch komutu ile karşılaştım.

tmpwatch komutu oldukça kullanışlı /tmp klasörü içinde dosyaların belli bir zamanda oluşmuşları yada hepsini silmenize yarıyor.

Kullanımı aşağıdaki gibidir.

```sh
tmpwatch [-u|-m|-c] [-MUadfqtvx] [–verbose] [–force] [–all] [–nodirs] [–nosymlinks] [–test] [–quiet] [–atime|–mtime|–ctime] [–dirmtime] [–exclude ] [–exclude-user ]
```

```sh
$ tmpwatch –mtime –all 366 /tmp
```
komutu 2 hafta içinde tmp klasörünün içinde oluşmuş olan dosyaları siliyor.(24*14 gün)

Klasör içindeki tüm içeriği silmesini istiyorsanız,

```sh
$ tmpwatch –mtime -all 0 /tmp
```
komutu ile klasör içeriğini tamamen silebilirsiniz.

Bu komutu sürekli elle girmektense cron ile istediğiniz zaman çalıştırarak temizleme işlemini yapabilirsiniz.

Bu komutu sürekli elle girmektense cron ile istediğiniz zaman çalıştırarak temizleme işlemini yapabilirsiniz.

http://linux.die.net/man/8/tmpwatch
