---
title: "Split - Dosyalari Parçalara Bölmek"
date: 2010-04-18T11:42:35+03:00
url: 2010/04/split-dosyalari-parcalara-bolmek
categories:
  - nix
tags:
  - split
  - split kullanımı
  - dosyaları parçalara bölmek
---
Elinizde büyük bir dosya var ve siz bunu hotfile yada rapid gibi sitelere yüklemek istiyorsunuz yalnız dosyanız çok büyük. Ne yapmanız gerekir diye düşünürken imdadınıza **“split”** komutu yetişiyor. Bu komut ile dosyanızı istediğiniz boyuttaki parçalara bölebilirsiniz.

```sh
$ split -b 10000k deneme.tar blok
```

deneme.tar.gz dosyasını 10Mb parçalar halinde blok adı altında split komutu size parçalara bölecektir.

Tekrar birleştirmek için ise

```
$ cat blok* > deneme.tar
```

komutunu vererek tekrar tek parça haline getirebilirsiniz.
