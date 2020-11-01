---
title: "Python Scripti İle Backup Tarihi Kontrolü"
date: 2013-07-26T10:46:41+03:00
url: 2013/07/python-scripti-ile-backup-tarihi-kontrolu
categories:
  - Python
  - Scripts
tags:
  - backup
  - backup oluşturulma tarihi
  - döngü
  - fonksiyon
  - oluşturulma tarihi
  - python
  - python ile mail gönderimi
  - python ile tarih kontrolü
  - script
  - sysadmin day
  - zaman kontrolü
---
Sunucu yedeklerinin locale alınması gerektiği ve  uzun süre takip edilemedi durumlarda, gereksiz disk kullanımının önüne geçmek ve ek önlem olarak backupların sunucunun localinin dışarısında başka bir yerde de tutmak ( sunucunun çökmesine, disklerin erişilemeyecek şekilde sorun olması durumuna karşın )  için, belli bir günden sonra birikmiş backupları belirlediğim mail adresine mail göndererek, backupları başka bir alana indirmem gerektiğini hatırlatan bir script yazmak istedim.

Script kısaca belirlediğiniz dizindeki dosyaların oluşturulma tarihleri ve bugünün tarihine bakıp, aradan 7 gün geçmiş ise scriptte belirtilen mail adresine bilgilendirme maili gönderiyor.

```sh
#!/usr/bin/env python
#-*- coding: UTF-8 -*-

import time
import os
import datetime
import smtplib

#sendemail fonksiyonunda smtpserver kısmına
# mail sunucusunun stmp bilgilerini girmeniz gerekiyor

def sendemail(from_addr, to_addr_list,
              subject, message,
              login, password,
              smtpserver='mail.domain.com:587'):
    header  = 'From: %s\n' % from_addr
    header += 'To: %s\n' % ','.join(to_addr_list)
    header += 'Subject: %s\n\n' % subject
    message = header + message

    server = smtplib.SMTP(smtpserver)
    server.starttls()
    server.login(login,password)
    problems = server.sendmail(from_addr, to_addr_list, message)
    server.quit()

#yedeklerin tutulduğu dizin
dir = "/home/yedek/"
# dizinde bulunan dosyaları listeliyoruz.
list = os.listdir(dir)

for i in list:
	# dosyaları tam yolları ile döngüye sokuyoruz
	fl = os.path.join(dir,i)
	# dosyaların olusturulma tarihini alıyoruz
	timestamp = time.ctime(os.path.getctime(fl))
	# sadece gununu belirleyebilmek için tuple formatına çeviriyoruz
    file_day  = time.strptime(timestamp)
	#sadece dosyanın gününü degiskene atıyoruz.        
    c_day = file_day.tm_mday

	#bugunun tarihini belirliyoruz
    date_today = datetime.date.today()
	#bugunun tarihinden sadece ayın kaçı olduğunu belirliyoruz	
    d_today = date_today.strftime("%d")	
	# dosya tarihi ile bugunun tarihini int e cevirip, farkını buluyoruz	
    hafta = int(d_today) - int(c_day)
	# eger dosya 7 gun once olusturulmuşsa sendemail fonksiyonuna 
        #git yoksa script sonlanacak		
	if hafta > 7:				
		continue
	else:
		quit()
# eger 7 günden fazla olmuş ise maili gönder
sendemail(from_addr = 'mailin gönderileceği adres', 
	to_addr_list = ['mailin alıcısı'], 
	subject      = 'Mail Başlığı', 
	message      = 'Mail içeriği', 
	login        = 'mailin gönderileceği adres', 
	password     = 'mail şifresi')
```

Bu scripti py uzantılı bir dosyaya kaydederek, cron aracılığı ile her gün çalıştırıp düzenli olarak yedek dizinini kontrol etmesini sağlayabilirsiniz.

> Güncelleme(29.07.2013):
> Özgür Savaşan Bey’in önerisi üzerine for döngüsünde timedelta kullanarak yazılmış hali,
> ```sh
for i in list:
	fl = os.path.join(dir,i)
	timestamp = os.path.getctime(fl)
	createdate = datetime.date.fromtimestamp(timestamp)
	today = datetime.date.today()
	today_before = today - datetime.timedelta(days = 7)
	if createdate == today_before:
		continue
	else:
		quit()
>```


Kaynaklar:

* http://www.pythonforbeginners.com/code-snippets-source-code/using-python-to-send-email/
* http://www.tutorialspoint.com/python/python_date_time.htm