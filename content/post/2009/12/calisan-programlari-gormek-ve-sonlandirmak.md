---
title: "Çalışan Programları Görmek ve Sonlandırmak"
date: 2009-12-20T11:57:00+03:00
url: 2009/12/calisan-programlari-gormek-ve-sonlandirmak
categories:
  - nix
tags:
  - ps
  - ps komutu
  - çalışan programları görmek
---

Linux da konsoldan herhangi bir programı sonlandırmak istediğinizde çalışan uygulamanın ilk önce  PID numarasını bilmeniz gerekirki uygulamayı sonlandırabilesiniz.

PID numarasını görmek için **”ps -aux”** komutunu kullanırız. Daha doğrusu bu komut arka tarafta çalışan bütün uygulamaları göstermeye yarar.

{{< img src="/images/ps.png" >}}

Burada gördüğünüz uygulamalar linux de arka planda çalışan ve sizin açtığınız uygulamaları göstermektedir. Herhangi bir sebebten kitlenmiş olan uygulamayı PID nuarasına bakarak **”kill PID_numarası”** yazarak sonlandırabilirsiniz.
