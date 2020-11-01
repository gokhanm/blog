---
title: "Script İle Konsolda Yazdığın Komutlari Otomatik Olarak Kaydetmek"
date: 2010-03-07T11:53:07+03:00
url: 2010/03/script-ile-konsolda-yazdigin-komutlari-otomatik-olarak-kaydetmek
categories:
  - nix
tags:
  - komut kaydetme
  - script
  - komut
  - otomatik komut kaydetme
---

Bugün karşılaştığım **“script”** komutundan bahsetmek istiyorum size.

Oldukça kullanışlı olan bu komut siz konsolda işlem yaparken o arka tarafta size konsolda yazdığınız komutları ve çıktı sonuçlarını belirlediğiniz bir dosyaya kaydedip daha sonra geri dönük olarak ne yapmıştım sıkıntısından kurtarıyor.

```sh
$ script -f dosya_ismi
```

vermeniz yeterli.

Script komutu arka tarafta konsolda görünen bütün sonuçları kaydederek daha sonra verdiğiniz dosya isminden okuyarak neler oluğ bittiğini görmenizi sağlıyor. Konsolda işiniz bittikten sonra **Ctrl-D** ye basarak scripti durdurmanız gerekiyor.

```sh
$ more dosya_ismi 
```

Komutu ilede çıktıyı okuyabilirsiniz.
