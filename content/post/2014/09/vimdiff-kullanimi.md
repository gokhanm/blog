---
title: "Vimdiff Kullanımı"
date: 2014-09-05T15:04:12+03:00
categories:
  - nix
  - LKD-Gezegen
tags:
  - diff
  - diff kullanımı
  - iki dosya arasındaki fark
  - iki dosya arasındaki farkı görmek
  - vim
  - vim diff mode
  - vimdiff
url: /2014/09/vimdiff-kullanimi
---
İki yada daha fazla txt dosyalarının, vim editörün diff mode özelliğini kullanarak diff farkını görmek için kullanılır. Vim text editörünün diff modda çalıştırılması ( vim -d ) ile vimdiff komutunu vererek çalıştırmak arasında bir fark yoktur. Aşağıdaki resimdeki gibi dosyalar açıldığında koyu kırmızı satırlar, iki dosya arasındaki diff farkını gösterir. **–#########–** şeklinde dosyaların başında ve sonunda görünmesinin sebebi, o satırların iki dosyada da aynı olmasıdır. Vimdiff, fark bulunmayan satırları göstermez.

```sh
# vimdiff file1 file2 [file3 [file4]]
# vim -d file1 file2 [file3 [file4]
```

httpd.conf ( resimde solda ) ve apachenin kaldırılması sonucunda oluşan httpd.conf.rpmsave dosyalarının aralarında diff farkına bakalım,

```sh
# vimdiff httpd.conf httpd.conf.rpmsave
```

{{< img src="/images/vim_diff.png" >}}

Editör içerisinde iken iki dosya arasında gezinmek için aşağıdaki kısayollardan biri kullanılabilir;

* Ctrl+W + Ctrl+W
* Ctrl+W + W

İki dosya arasındaki farkları uygulamak için aşağıdaki editör komutları yada kısayolları kullanılır,

* :[range]diffput ( yada dp kısayolu ), imlecin bulunduğu satırındaki değişikliği karşı dosyaya uygulayarak, karşı dosyanın aynı satırını değiştirir. ( put )
* :[range]diffget ( yada do kısayolu ), imlecin bulunduğu satırı, karşıdaki dosyanın aynı satırına göre, imclecin bulunduğu satırı değiştirir. ( get )

diffput yada diffget komutların başlarında range kullanılmaması durumunda, farklı satırların alt alta olması durumunda vim grup olarak  değişiklik uygular. Sadece satırda değişiklik yapmak için diffget ve diffput aşağıdaki şekilde kullanılır.

* :39,diffget  ; 39. satırı karşı dosyadaki aynı satıra göre imlecin bulunduğu satırı değiştirir.
* :299,diffput ; Karşı dosyadaki 299. satırı değiştir.
* :200,$diffget ; 200. satırdan sonra son satıra kadar değişiklikleri uygular.

Vim diff mode özelliğini daha ayrıntılı incelemek için [man](http://linux.die.net/man/1/vimdiff) sayfasına bakabilirsiniz.