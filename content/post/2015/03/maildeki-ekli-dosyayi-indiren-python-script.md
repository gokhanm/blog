---
title: "Maildeki Ekli Dosyayı İndiren Python Script"
date: 2015-03-01T14:51:41+03:00
categories:
  - Python
tags:
  - attachment
  - ekli dosya
  - mail silme
  - maildeki ekli dosyaları indiren script
  - okunan mailleri silen script
  - python
  - script
  - seen
  - unseen
url: /2015/03/maildeki-ekli-dosyayi-indiren-python-script
---
Mail içerisinde gelen ekli dosyayı IMAP SSL bağlantısı sağlayarak indirdikten sonra, okundu olarak işaretleyip, okundu olarak işaretlenen maillerin deleted olarak etiketlenmesini sağlayan python scripti.

```python
#!/usr/bin/env python

import email
import getpass
import imaplib
import os

detach_dir = '.'  # directory where to save attachments (default: current)
user = "username"
pwd = "password"

# connecting to the gmail imap server
m = imaplib.IMAP4_SSL("mail.domain.com")
m.login(user, pwd)
m.select("INBOX")  # here you a can choose a mail box like INBOX instead
# use m.list() to get all the mailboxes

# you could filter using the IMAP rules here (check http://www.example-code.com/csharp/imap-search-critera.asp)
resp, items = m.search(None, 'UNSEEN')
items = items[0].split()  # getting the mails id

for email_id in items:
    # fetching the mail, "`(RFC822)`" means "get the whole stuff", but you can ask for headers only, etc
    resp, data = m.fetch(email_id, "(RFC822)")
    email_body = data[0][1]  # getting the mail content
    mail = email.message_from_string(email_body)  # parsing the mail content to get a mail object

    # Check if any attachments at all
    if mail.get_content_maintype() != 'multipart':
        continue

    print "["+mail["From"]+"] :" + mail["Subject"]

    # we use walk to create a generator so we can iterate on the parts and forget about the recursive headach
    for part in mail.walk():
        # multipart are just containers, so we skip them
        if part.get_content_maintype() == 'multipart':
            continue

        # is this part an attachment ?
        if part.get('Content-Disposition') is None:
            continue

        filename = part.get_filename()
        counter = 1

        # if there is no filename, we create one with a counter to avoid duplicates
        if not filename:
            filename = 'part-%03d%s' % (counter, 'bin')
            counter += 1

        att_path = os.path.join(detach_dir, filename)

        # Check if its already there
        if not os.path.isfile(att_path):
            # finally write the stuff
            fp = open(att_path, 'wb')
            fp.write(part.get_payload(decode=True))
            fp.close()

# flag as deleted
dresp, ditems = m.search(None, 'SEEN')
ditems = ditems[0].split()
for demail_id in ditems:
    m.store(demail_id, '+FLAGS', '\\Deleted')
    m.expunge()
```