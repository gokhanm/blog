---
title: "Postfix,mysql,dovecot,tls,ssl Postgrey Kurulumu ve Yapılandırması"
date: 2011-08-21T14:06:53+03:00
url: 2011/08/postfixmysqldovecottlsssl-postgrey-kurulumu-ve-yapilandirmasi
categories:
  - Postfix
tags:
  - dovecot
  - mail sunucusu kurulumu
  - postfix
  - postfixadmin
  - postgrey
---

Bu yazıda postfix mail server ile birlikte Dovecot kurulumunu deneyeceğiz. Bunun yanında sanal kullanıcıların saklanması için mysql serverını kullanacağız. Sanal domainleri ve kullanıcıları oluşturabilmeniz için postfix admin den yararlanacağız. Bütün bu kurulumlar RedHat,Centos 5.x için anlatılacaktır.

## Postfix Kurulumu

```sh
# yum install postfix
```
komutu ile postfixi çok rahat bir şekilde kurabilirsiniz. Yalnız biz mail server kurulumumuzda mysql üzerinde sanal kullanıcıları tutacağımız için yukarıdaki komut ile kurmanız halinde postfix mysql desteği ile kurulmamış olacaktır. Sonuçta herşey tamam olsa bile postfixin mysqle bağlanamamasından dolayı mail sunucusu çalışmayacaktır.


Postfixi mysql desteği ile kurmak için yum.repo.d üzerinde bir kaç düzenleme yapmamız gerekiyor.CentOS-Base.repo dosyasının base,update ve centosplus kısımlarını aşağıdaki gibi düzenledikten sonra postfix kurulumu yapıldığında mysql desteği gelmiş olacaktır.

```sh
# nano /etc/yum.repos.d/CentOS-Base.repo

[base]

exclude=postfix

[update]

exclude=postfix

[centosplus]

enabled=1

includepkgs=postfix
```
```sh
# yum install postfix
# postconf -m
btree
cidr
environ
hash
ldap
mysql
nis
pcre
pgsql
proxy
regexp
static
unix
```

Postfix kurulumumuz tamam şimdi main.cf dosyasını düzenlememiz gerekiyor.

```sh
# nano /etc/postfix/main.cf

smtpd_banner = $myhostname ESMTP $mail_name (Debian/GNU)

append_dot_mydomain = no
#delay_warning_time = 4h
myhostname = mail.domain.com
myorigin = domain.com
mydestination = localhost
mynetworks = 127.0.0.0/8
mailbox_size_limit = 0
recipient_delimiter = +
inet_interfaces = all
```
myhostname ve myorigin kısımlarını kendi domaininize göre düzenlemeyi unutmayınız.

## MySQL Yapılandırması

Birçok sunucuda mysql kurulu olarak gelmektedir. İlk kez kuracak iseniz;

```sh
# yum install mysql-server mysql-client
```

Artık database oluşturmaya geldi. Postfix,dovecot ve postfix admin için sadece bir db oluşturmanız yeterli. Oluşturulan databasede postfix ve dovecot sql komutu olan SELECT, postfix admin ise SELECT, UPDATE  ve INSERT komutlarını kullanacak.

```sh
# mysql -uroot -p

mysql> CREATE DATABASE db_ismi;
mysql> GRANT ALL PRIVILEGES ON db_ismi.* TO ‘kullanıcı_adı’@’localhost’ IDENTIFIED BY ‘şifre’;
mysql> quit;
```

## Sanal Kullanıcılar

Yazının en başında da dediğimiz gibi sanal kullanıcıları mysql üzerinde tutacağız. Bunun postfix main.cf dosyasına bir kaç satır eklememiz gerekiyor.

```sh
# Virtual mailbox settings

virtual_mailbox_domains = mysql:$config_directory/mysql_virtual_domains_maps.cf
virtual_mailbox_base = /var/vmail
virtual_mailbox_maps = mysql:$config_directory/mysql_virtual_mailbox_maps.cf
virtual_alias_maps = mysql:$config_directory/mysql_virtual_alias_maps.cf
virtual_minimum_uid = 150
virtual_uid_maps = static:150
virtual_gid_maps = static:8
virtual_transport = dovecot
dovecot_destination_recipient_limit = 1
# SASL Authentication
smtpd_sasl_auth_enable = yes
smtpd_sasl_exceptions_networks = $mynetworks
smtpd_sasl_security_options = noanonymous
broken_sasl_auth_clients = yes
smtpd_sasl_type = dovecot
smtpd_sasl_path = private/auth
```

Sanal kullanıcıların mailbox larını /var/vmail altında tutacağız bunun için vmail kullanıcısı ve mail adında bir group oluşturmamız gerekiyor.

```sh
# useradd -r -u 150 -g mail -d /var/vmail -s /sbin/nologin -c “Virtual mailbox” vmail

# mkdir /var/vmail
# chmod 770 /var/vmail/
# chown vmail:mail /var/vmail/
```
Ayrıca sadece izin verilmiş kullanıcıların mail atabilmesi için main.cf dosyasına rule yazmamız gerekiyor.

```sh
smtpd_recipient_restrictions =

permit_mynetworks
permit_sasl_authenticated
reject_unauth_destination
permit
```

## Postfix Mysql Ayarları

Postfixin mysql de kayıtlı bulunan bilgilerin nerede ve nasıl okuması gerektiğini tanımlamamız gerekiyor. Bunun için /etc/postfix altına birkaç dosya oluşturup düzenlememiz gerekiyor.

```sh
# nano /etc/postfix/mysql_virtual_alias_maps.cf

user = db_kullanıcı_adi
password = şifre
hosts = localhost
dbname = db_ismi

query = SELECT goto FROM alias WHERE address=’%s’ AND active = ’1′

# nano /etc/postfix/mysql_virtual_domains_maps.cf

user = db_kullanıcı_adi
password = şifre
hosts = localhost
dbname = db_ismi
query = SELECT domain FROM domain WHERE domain=’%s’ AND backupmx = ’0′ AND active = ’1′

# nano /etc/postfix/mysql_virtual_mailbox_limit_maps.cf

user = db_kullanıcı_adi
password = şifre
hosts = localhost
dbname = db_ismi
query = SELECT quota FROM mailbox WHERE username=’%s’ AND active = ’1′

# nano /etc/postfix/mysql_virtual_mailbox_maps.cf

user = db_kullanıcı_adi
password = şifre
hosts = localhost
dbname = db_ismi

query = SELECT CONCAT(domain,’/’,maildir) FROM mailbox WHERE username=’%s’ AND active = ’1′
```

## Dovecot Kurulumu ve Ayarları

IMAP ve Pop için dovecot kullanacağız.

```sh
# yum install dovecot
```

Postfix in dovecot ile anlaşa bilmesi için postfix master.cf dosyasının en altına aşağıdaki satırı eklememiz gerekiyor.

```sh
dovecot unix – n n – – pipe flags=DRhu user=vmail:mail argv=/usr/lib/dovecot/deliver -d $(recipient)
```

Dovecot kurulumundan sonra /etc/dovecot.conf dosyasını aşağıdaki şekilde düzenlememiz gerekiyor.

Dovecot.conf dosyasında ssl ve dovecot-sql.conf ayarlamalarını bilerleyen kısımlarda anlatacağım.

```sh
## Dovecot configuration file
#
#
# imap imaps pop3 pop3s (use imaps and pop3s if configured for SSL)
protocols = imap pop3
#
# Uncomment the ssl_listen statements and comment out listen if using SSL
protocol imap {
listen = *:143
# ssl_listen = *:993
}
protocol pop3 {
listen = *:110
# ssl_listen = *:995
}
#
log_timestamp = “%Y-%m-%d %H:%M:%S ”
syslog_facility = mail
#
# Uncomment these if using SSL
ssl_cert_file = /etc/ssl/domain.com/mailserver/mail-cert.pem
ssl_key_file = /etc/ssl/domain.com/mailserver/mail-key.pem
ssl_ca_file = /etc/ssl/domain.com/ca/mycompany.pem
ssl_verify_client_cert = yes
ssl_parameters_regenerate = 168
verbose_ssl = no
#
# Where the mailboxes are located
mail_location = maildir:/var/vmail/%d/%u
#
mail_extra_groups = mail
mail_debug = no
first_valid_uid = 150
last_valid_uid = 150
maildir_copy_with_hardlinks = yes
#
protocol imap {
login_executable = /usr/libexec/dovecot/imap-login
mail_executable = /usr/libexec/dovecot/imap
imap_max_line_length = 65536
}
protocol pop3 {
login_executable = /usr/libexec/dovecot/pop3-login
mail_executable = /usr/libexec/dovecot/pop3
pop3_uidl_format = %08Xu%08Xv
}
#
auth_verbose = no
auth_debug = no
auth_debug_passwords = no
#
auth default {
mechanisms = plain
passdb sql {
args = /etc/dovecot-sql.conf
}
userdb sql {
args = /etc/dovecot-sql.conf
}
user = nobody
socket listen {
master {
path = /var/run/dovecot/auth-master
mode = 0660
user = vmail
group = mail
}
client {
path = /var/spool/postfix/private/auth
mode = 0660
user = postfix
group = postfix
}
}
#
# If you want client certificates, use these lines
# ssl_require_client_cert = yes
# ssl_username_from_cert = yes
}
```

Dovecotun sql ile konuşabilmesi için dovecot-sql.conf adında bir dosya oluştup içeriğini aşağıdaki gibi ayarlıyoruz.

```sh
# nano /etc/dovecot-sql.conf

driver = mysql

connect = host=localhost dbname=db_ismi user=db_kullanıcı_ismi password=şifre

default_pass_scheme = MD5

user_query = SELECT ‘/var/vmail/%d/%n’ as home, ‘maildir:/var/vmail/%d/%n’ as mail, 150 AS uid, 8 AS gid, concat(‘dirsize:storage=’, quota) AS quota FROM mailbox WHERE username = ‘%u’ AND active = ’1′

password_query = SELECT username as user, password, ‘/var/vmail/%d/%n’ as userdb_home, ‘maildir:/var/vmail/%d/%n’ as userdb_mail, 150 as userdb_uid, 8 as userdb_gid FROM mailbox WHERE username = ‘%u’ AND active = ’1′

# chmod 600 /etc/dovecot-sql.conf
# chown vmail /etc/dovecot-sql.conf
```

dovecot ayarları bitti SSL için düzenlemeyi daha sonra yapacağız.

## Postfix admin Kurulumu ve Yapılandırması

Postfix adminin düzgün çalışabilmesi için bir web sunucusuna ( apache vb.) ve php kurulumuna ihtiyacınız bulunuyor. Bunların olduğunu düşünerek postfix admin kurulumunun ardından imap hatası aırsanız aşağıdaki komutu çalıştırarak php-imap I kurmanız gerekiyor.

```sh
# yum install php-imap
```
Ben postfixadmini bir domainin altında çalıştıracağım için postfixadmin kurulum dosyalarını /var/www/html/domain.com altında barındıracağım.

Artık postfix admini kurabiliriz.

http://sourceforge.net/projects/postfixadmin/

Yukarıdaki adres yardımıyla postfixadminin son sürümünü indirin.

```sh
# tar xvfz postfixadmin-2.3.3.tgz
```

Sıkıştırılmış  dosyayı açtıktan sonra config.inc.php dosyasını düzenlememiz gerekiyor.

```sh
$CONF[‘postfix_admin_url’] = ‘/var/www/domain.com/postfixadmin/’;

$CONF[‘postfix_admin_path’] = ‘http://localhost/postfixadmin’;
$CONF[‘database_host] = ‘localhost’;
$CONF[‘database_user’] = ‘db_kullanıcı_adi’;
$CONF[‘database_password’] = ‘şifre’;
$CONF[‘database_name’] = ‘db_ismi’;
```

## TLS ve SSL kurulumu

self-signed sertifikas kurulumu anlatılacak olup. Mail programları üzerinden ilk bağlantınızda güvenilmeyen ssl uyarısı almanız kaçılımazdır.

İlk once ssl sertifikasını tutacağımız yeri belirleyip. Bu klasörün içerisine pem dosyalarını oluşturacağız.

```sh
# mkdir -p /etc/ssl/domain.com/mailserver/

# cd /etc/ssl/domain.com/mailserver/
# openssl genrsa 1024 > mail-key.pem
# chmod 400 mail-key.pem
# openssl req -new -x509 -nodes -sha1 -days 365 -key mail-key.pem > mail-cert.pem
```

Mail-cert.pem sertifikası için bir kaç soru soracak bunların içerisinden Common Name (eg, YOUR name) için istenen bilgi sunucu bilginiz ile aynı yapmanız tavsite ediliyor. Örneğin mail.domain.com

## Dovecot SSL yapılandırması

SSL tanımlamaları bittikten sonra bu bilgileri dovecot ve postfix e tanımlamamız gerekiyor. Ki bunu daha önceden dovecot.conf dosyasını düzenlerken ssl bilgilerinide düzenlemiştik.

```sh
protocols

ssl_listen
ssl_cert_file
ssl_key_file
ssl_parameters_regenerate = 168
verbose_ssl = no
````

Dovecot şu an 993 ssl port isteklerine cevap verecektir.Aşağıdaki gibi bir log çıktısı görünecektir.

```sh
dovecot: imap-login: Login: user=, method=PLAIN, rip=127.0.1.1, lip=127.0.1.1, TLS
```

Postfix TLS ayarları için main.cf dosyasına aşağıdaki bilgileri girmemiz gerekiyor.

```sh
smtpd_tls_cert_file = /etc/ssl/domain.com/mailserver/mail-cert.pem

smtpd_tls_key_file = /etc/ssl/domain.com/mailserver/mail-key.pem
smtpd_tls_session_cache_database = btree:/var/spool/postfix/smtpd_tls_session_cache
smtpd_tls_security_level = may
smtpd_tls_received_header = no
smtpd_tls_loglevel = 0
tls_random_source = dev:/dev/urandom
```

## Postgrey Yapılandırması ve Kurulumu

Herkez spamlardan nefret eder ve bunları engellemek için bir çok yöntem denemektedir. Postfix ile en çok kullanılan spam engelleme aracı olan postgreyi kurup, yapılandıracağız.

```sh
# yum install postgrey
```

Postfrey için postfixe smtpd_recipient_restrictions kuralı yazarken son kural olan permit ten önce yazmaya dikkat ediyoruz. Buda postfix mailleri dovecota iletmeden once postgreyin elinden geçerek spam olarak iletilmesini engelleyecektir.

```sh
check_policy_service inet:127.0.0.1:60000

main.cf dosyasına ekleyeceğimiz rule;

smtpd_recipient_restrictions =

permit_mynetworks
permit_tls_all_clientcerts
#permit_sasl_authenticated
reject_non_fqdn_hostname
reject_non_fqdn_sender
reject_non_fqdn_recipient
reject_unauth_destination
reject_unauth_pipelining
reject_invalid_hostname
#reject_unknown_sender_domain
#reject_unknown_hostname
reject_rbl_client zen.spamhaus.org
reject_rbl_client bl.spamcop.net
reject_rbl_client cbl.abuseat.org
reject_rbl_client dnsbl.njabl.org
reject_rbl_client dnsbl.sorbs.net
reject_rhsbl_sender dsn.rfc-ignorant.org
check_policy_service inet:127.0.0.1:60000
permit

#
smtpd_data_restrictions =
reject_unauth_pipelining,
reject_multi_recipient_bounce,
permit
```

Burada reject_non_fqdn kısımlarını isterseniz kullanmaya bilirsiniz. Bu kurallar ters isim güncellemesi olmayan domainlerden mail gelmesini engelleyecektir. Buda mail spam olmasa bile engellenmesi demektir.

main.cf dosyasına yazdığımız check_policy_service inet:127.0.0.1:60000 kuralının çalışabilmesi için postgreye  inet:127.0.0.1:60000 i dinlemesini sölememiz gerekiyor. Bunu içinde aşağıdaki adımı uygulayabilirsiniz.

```sh
# nano /etc/init.d/postgrey
```
Dosyasını açarak,

**OPTIONS=”–unix=$SOCKET”**  şeklinde yazılmış satırı **OPTIONS=”–inet=127.0.0.1:60000 -d”** şekilde değiştirmeniz gerekiyor.

```sh
# /etc/init.d/postgrey restart
```
