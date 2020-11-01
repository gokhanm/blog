---
title: "Google Reader'dan Sonra..."
date: 2013-03-24T11:38:42+03:00
url: 2013/03/google-readerdan-sonra
categories:
  - Genel
tags:
  - feed
  - feedly
  - good noows
  - google
  - google currents
  - google reader
  - google reader alternatif
  - lilina
  - pulse
  - reader
  - rss
  - rss feed
  - selfoss
---

Google Reader’ın yaşam hayatına son verileceğinin [duyurulmasından](http://googlereader.blogspot.com/2013/03/powering-down-google-reader.html) sonra, rss kullanıcıları birçok alternatifleri araştırmaya başladı.

Ben de bu alternatiflerden, bizzat kullandığım, rss hizmeti veren sitelerin yanında kendi sunucunuza kurabileceğiniz birkaç alternatifi paylaşmak istedim.

## [Selfoss](http://selfoss.aditu.de/)
Kendi rss’imi kendim yönetmek istiyorum diyenler için oldukça güzel bir Rss Feed Scripti. Web tabanlı ve mobile olarak çok rahat kullanabileceğiniz bir arayüze sahip. Mysql, Postgresql ve Sqlite databaselerini destekleyen script aynı zamanda Google Reader’dan alacağınız OPML ile Rsslerinizi yükleme imkanıda sunmakta.

{{< img src="/images/selfoss.png" >}}

Selfoss’un eksilerinden bahsetmek gerekirse, rss leri okumak için belirlediğiniz domain adresine girdiğinizde herhangi bir login ekranı gelmiyor. Buda domain adresini bilen biri tarafından takip ettiğiniz sitelerin rss lerini okuyabilir anlamına geliyor. Bir diğer dezavantaj ise, takip ettiğiniz rssleri kendi sunucunuzda barındırdığınız için, bir müddet sonra database şişmesi, yavaşlaması, disk alanı problemleri gibi durumla karşılaşabilirsiniz. Birde yeni rss leri çekebilmesi için cron yazmanız gerekiyor.

## [Lilina](http://getlilina.org/)
Bir diğer kendi sunucunuzda Rss lerl okuyabileceğiniz script olan lilina, selfosstaki gibi database kullanmayıp, tüm rssleri disk üzerinde bir dosyada tutmakta. Buda şöle bir dezavantaj getiriyor, bir databaseden rss leri çekmediği için, yeni gelen rss lerin hangisi olduğunu göstermeyip, tüm rss leri ekrana basıyor.

{{< img src="/images/lilina.png" >}}

## [Feedly](http://www.feedly.com/)
Her teknoloji blog yazısında karşılaşacağınız feedly, google reader hesabınıza erişip, tüm rss kaynaklarınızı google reader üzerinden çekerek size sunmakta. Bunun için feedly’e google reader’a erişebilmesi için izin vermeniz gerekiyor. Güvenlik konusunda paranoyak olanlar için, ne işi var benim google hesabımda diyebilir. Bu yüzden hangi alanlara erişim istediğini okumanız faydalı olacaktır. Feedly ile Google Reader birbirine senkron çalıştığı için Feedly de okuduğunuz bir yazı, google readerda da okunmuş olarak sayılıyor.

Firefox, Chrome ve mobil uygulaması bulunan feedly nin, tarayıcılara eklenti olarak kurulması gerekiyor. Sitesi üzernden herhangi bir login ekranı bulunmayıp, sizi eklentilere yönlendiriyor.

{{< img src="/images/feedly.jpg" >}}

Google Reader’a oldukça alışmış biri olarak feedlye ısınmak biraz zaman alacak gibi, daha iyisi çıkana kadar sanırım google reader alternatifi olarak en gözde site olarak görünüyor. Ayrıca feedly, Google Reader’ı kapadıktan sonrada rss lerin Google Reader’dan bağımsız olarak çalışması için gerekli çalışmalara başladığını duyurdu.

## [Goog Noows](http://goodnoows.com/)

Haber rss lerini takip edebileceğiniz, kullanıcı arayüzü oldukça güzel olan bir site. Ayrıca Google Reader dan aldığınız OPML dosyasını yüklemenize imkan veren bir site. En önemli eksiği mobil arayüzü yada mobil uygulamasının olmaması. Bu yüzden bir süre kullandıktan sonra tekrar feedly’e dönmek zorunda kalmam oldukça üzücü bir durum. Ayrıca çok fazla RSS kaynağınız varsa, sitenin yüklenmesi oldukça zaman almakta.

{{< img src="/images/goodnoows.png" >}}

Yukarıda bahsettiğim google reader alternatifleri dışında tabiki başka sitelerde mevcut. Google Reader’ın kapanacağı duyurulduktan sonra bu tür servislerinde artacağı aşikar.

{{< img src="/images/google_reader.png" >}}

Google birçok servisini zaman içerisinde kapatmıştır. Ama insanlarda google reader gibi çok kullandığı bir servisi kapatması hayal kırıklığına sebebiyet verdi ve google’a kendi Google Keep üzerinden güzel göndermeler yapmaya başladılar. Ayrıca Google Reader’ın kapanmaması için siteler[[1]](http://bringgooglereaderback.com/) yapmaya başladılar.


Tahmin ediyorumki, Google’ın Google Readerı kapatmasındaki en büyük etken geçen sene duyurduğu [Google Currents](https://www.google.com/producer/currents) adlı servisinden kaynaklanıyor. Google’ın bu servisinde de [Pulse](https://www.pulse.me/) vb gibi haber kaynakları okuyabileceğiniz bir servis olmasının yanında RSS lerinizide takip edebilmenize imkan veriyor.


