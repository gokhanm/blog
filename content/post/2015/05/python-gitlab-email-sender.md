---
title: "Python - Gitlab Email Sender"
date: 2015-05-31T13:45:47+03:00
categories:
  - LKD-Gezegen
  - Python
tags:
  - gitlab
  - gitlab hook
  - gitlab send email
  - python
  - python gitlab hook
url: /2015/05/python-gitlab-email-sender
---
Gitlab kullanıyor iseniz, gitlab sunucunuzdaki post-receive hook scriptinin içerisine, aşağıdaki python scriptini her push işleminden sonra çalıştıracak şekilde ayarlamanız durumunda, script her çalıştığında oluşturduğu **commits.db** sqlite database’indeki short commit’i ile karşılaştırıp, değişikli var ise aşağıdaki mail gönderim formatında maili **‘to@domain.com’** adresine gönderir. Eğer db’de bulunmayan bir repo ile karşılaşması durumunda db’ye repo’yu insert eder.

```python
#!/usr/bin/env python
__author__ = "Gokhan MANKARA <gokhan@mankara.org"

import gitlab
import sqlite3 as sql
import requests
import smtplib
import os
import email.utils
from email.mime.text import MIMEText

gitlab_url = "https://gitlab.domain.com"
gitlab_access_token = "ABCDEFG"


class SendEmail(object):
    def __init__(self):
        self.mail_to = 'to@domain.com'
        self.mail_from = 'from@domain.com'
        self.mail_server = 'mail.domain.com'
        self.mail_subject = 'New Commit'

    def main(self, msg):
        msg = MIMEText(msg)
        msg['To'] = email.utils.formataddr(('Recipient', self.mail_to))
        msg['From'] = email.utils.formataddr((self.mail_subject, self.mail_from))
        msg['Subject'] = self.mail_subject

        server = smtplib.SMTP(self.mail_server, 25)
        server.set_debuglevel(False)

        try:
            server.sendmail(self.mail_from, self.mail_to, msg.as_string())
        finally:
            server.quit()


class Commit(object):
    def __init__(self):
        self.db = "commits.db"
        self.con = sql.connect(self.db)
        self.git = gitlab.Gitlab(gitlab_url, token=gitlab_access_token, verify_ssl=False)
        with self.con:
            self.cur = self.con.cursor()
        requests.packages.urllib3.disable_warnings()

        if not os.path.isfile(self.db):
            os.system("sqlite3 commits.db < commits.sql")

    def sql_insert(self, repo, branch, hash):
        sql = "insert into last_commit ('repo', 'branch', 'hash') values ('%s', '%s', '%s')" % (repo, branch, hash)
        self.cur.execute(sql)
        self.con.commit()

    def sql_select(self, branch, repo):
        sql = "select hash from `last_commit` where branch='%s' and repo='%s'" % (branch, repo)
        self.cur.execute(sql)
        return self.cur.fetchone()[0]

    def sql_update(self, new_hash, branch, repo):
        sql = "UPDATE last_commit SET hash='%s' WHERE branch='%s' and repo='%s'" % (new_hash, branch, repo)
        self.cur.execute(sql)
        self.con.commit()

    def commits(self):
        # git.getprojectsall => Returns a dictionary of all the projects for admins only
        # git.getprojects => Returns a dictionary of all the projects

        for i in self.git.getprojectsall():
            group_name = i["namespace"]["name"]
            project_name = i["path"]
            project_id = i["id"]
            for branches in self.git.getbranches(project_id):
                    new_commit = branches["commit"]["id"][:8]
                    branch_name = branches["name"]

                    try:
                        commit_in_sql = self.sql_select(branch_name, project_name)
                    except TypeError:
                        self.sql_insert(project_name, branch_name, new_commit)

                    if commit_in_sql != new_commit:
                        get_commit_info = self.git.getrepositorycommit(project_id, new_commit)
                        try:
                            author_name = get_commit_info["author_name"]
                            author_email = get_commit_info["author_email"]
                            author_message = get_commit_info["title"]
                            commit_date = get_commit_info["created_at"]
                        except TypeError:
                            pass

                        compare_url = "%s/%s/%s/compare/%s...%s" %\
                                      (gitlab_url, group_name, project_name, check_commit, new_commit)
                        send_email = SendEmail()
                        msg = 'Commit Repo: %s\nBranch Name: %s\nUser: %s\nUser Mail: %s\nCommit Message: %sCommit Time: %s\n\nDiff URL: %s' %\
                              (project_name, branch_name, author_name, author_email, author_message, commit_date, compare_url)
                        send_email.main(msg)
                        self.sql_update(new_commit, branch_name, project_name)

    def main(self):
        self.commits()


if __name__ == "__main__":
    Commit().main()
```

Python scripti ile aynı yerde oluşturmanız gereken **commits.sql** dosyası dosyası.

```sh
Commit Repo: githlab-email-repo
Branch Name: master
User: gokhan
User Mail: gokhan@mankara.org
Commit Message: First Test Email Commit
Commit Time: 2015-05-30T13:58:42.000+03:00
 
Diff URL: https://gitlab.domain.com/gokhan/gitlab-email-repo/compare/s1ws93...1ex4ng59
```

Gitlab server’da post-receive hook scriptine, başına **/usr/bin/python** koyarak scripti kullanabilirsiniz. Başına python path’ini koymamızın sebebi, gitlab’ın kendi python binary si olduğundan import ettiğimiz modüller gitlab’ın kendi lib’inde olmadığı için remote gitlab server’a push ettiğinizde “modül bulunamadı” şeklinde hata verecektir.

[Gitlab Repo](https://github.com/gokhanm/gitlab-email-hook)
