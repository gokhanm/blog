---
title: "Asithappens Gerçek Zamanli Network Monitörleme Aracı"
date: 2014-01-03T13:39:38+03:00
url: 2014/01/asithappens-gercek-zamanli-network-monitorleme-araci
categories:
  - nix
  - LKD-Gezegen
tags:
  - asithappens
  - gerçek zamanlı
  - grafik
  - network
  - network monitoring tool
  - trafik monitörleme aracı
---
Windowsda çalışan stg programının linux alternatifini ararken karşılaşığım oldukça kullanışlı network monitör programı olan [AsItHappens](http://www.abrahams.co.nz/asithappens/), GNU lisansı ile dağıtılan, ayrıca windows versiyonuda bulunan,  gerçek zamanlı network istatistik grafiğini alabileceğiniz, java tabanlı bir network aracı.

OID, Network Response, işlemci ve ram kullanımı gibi bir çok özelliği SNMP üzerinden okuyabilme özelliğine sahip.

Kurulum gereksinimi olarak sistemde,  Java Runtime Environment (JRE) ya da Java Development Kit (JDK) version 6 veya üstü bir versiyonun kurulu olması gerekiyor.

Linux kurulum dosyasını çalıştırdığınız karşınız resimdeki şekilde bir kurulum ekranı gelmekte. Next diyerek kurulum adımlarını geçip, kurulumu tamamlıyorsunuz.

{{< img src="/images/kurulum.png" >}}

Kurulum tamamlandıktan sonra, Start-Menu ve masaüstüne bir kısayol oluşturarak çalıştırıla bildiğini sölesede, kurulumda kısayolu oluşturabilmiş değil. En güzel yöntem terminal üzerinden bash scriptini ( kurulum yaptığınız dizinin altında **asithappens/bin/asithappens.sh** olarak geçer ) çalıştırarak programın çalışmasını sağlayabilirsiniz.

{{< img src="/images/asithappend-menu.png" >}}

Benim kullandığım Bandwidth Graph özelliği, cihazın SNMP özelliği sayesinde seçtiğiniz port üzerinden aldığı giriş ve çıkış trafiğini yorumlayıp aşağıdaki şekilde grafik haline dönüştürüyor.

{{< img src="/images/asithappens-grap.png" >}}
