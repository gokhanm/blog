---
title: "Python Watchdog İle Dizin İzleme"
date: 2015-03-28T14:01:11+03:00
categories:
  - Python
tags:
  - bilgilendirme
  - mail
  - notification
  - python
  - python ile mail gönderimi
  - watchdog
url: /2015/03/python-watchdog-ile-dizin-izleme
---

Script real time olarak, arguman verdiğiniz path’i ve alt klasörlerini izleyerek, yeni oluşan yada silinen dosyaları bilgilendirme maili gönderen python scripti. Cron’a ekleyerekte arka planda sürekli çalışmasını sağlayabilirsiniz. Cron kullanımı hakkında bilginiz yok ise, bu yazımı inceleyebilirsiniz.

```sh
./check_dir.py /home/gokhan/Desktop
```

Mail içeriği,
```
/home/gokhan/Desktop/file-5.22-1.fc22.src.rpm
 
deleted:
file-5.22-1.fc22.src.rpm
```

```python
#!/usr/bin/env python3.4
__author__ = 'Gokhan MANKARA <gokhan@mankara.org>'

import time
import sys
import smtplib
import socket
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
from watchdog.observers import Observer
from watchdog.events import FileSystemEventHandler


class Check(FileSystemEventHandler):

    def send_mail(self, events, files):
        msg = MIMEMultipart()
        msg['From'] = 'from@domain.com'
        msg['To'] = 'to@domain.com'
        msg['Subject'] = 'Detected changes in the folder'
        text = "{0}\n\n{1}:\n{2}".format(events.src_path, events.event_type, files)
        msg.attach(MIMEText(text))

        mail_server = smtplib.SMTP('mail.domain.com', 587)
        mail_server.ehlo()
        mail_server.starttls()
        mail_server.login('from@domain.com', 'password')

        mail_server.sendmail('from@domain.com', 'to@domain.com', msg.as_string())
        mail_server.quit()

    def process(self, event):
        """
        event.event_type
            'modified' | 'created' | 'moved' | 'deleted'
        event.is_directory
            True | False
        event.src_path
            path/to/observed/file
        """

        files = event.src_path.split('/')[-1:][0]
        self.send_mail(event, files)

    def on_deleted(self, event):
        self.process(event)

    def on_created(self, event):
        self.process(event)

    def on_modified(self, event):
        self.process(event)


def lock(pidfile):
    pid = str(os.getpid())
    if os.path.isfile(pidfile):
        sys.exit()
    else:
        open(pidfile, 'w').write(pid)

if __name__ == '__main__':
    args = sys.argv[1:]
    observer = Observer()
    observer.schedule(Check(), path=args[0] if args else '.', recursive=True)
    observer.start()

    try:
        lock('/tmp/check_dir.pid')
        while True:
            time.sleep(1)
    except KeyboardInterrupt:
        observer.stop()
        os.unlink('/tmp/check_dir.pid')
    observer.join()
```