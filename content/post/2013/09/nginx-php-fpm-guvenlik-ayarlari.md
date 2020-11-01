---
title: "Nginx Php Fpm Güvenlik Ayarları"
date: 2013-09-22T15:40:11+03:00
url: 2013/09/nginx-php-fpm-guvenlik-ayarlari
categories:
  - nix
tags:
  - centos 6.4
  - fastcgi_param
  - güvenlik ayarı
  - nginx
  - nginx güvenlik ayarı
  - nginx ve php-fpm güvenlik
  - open_basedir
  - php-fpm
  - php-fpm pool yapılandırması
---
Web sunucu olarak kullandığınız nginx, php-fpm bir sunucuda, bir web sitesinde meydana gelen güvenlik açıklarından diğer sitelerinde etkilenmemesini isteyebilirsiniz. Bu durumda Apache’de kullanılan suphp yada suexec benzeri bir yapıyı php-fpm’de de kullanabilirsiniz. Bu yapı Apache/PHP deki gibi php’yi suphp/suexec olarak derlemek yerine, php-fpm’de kullanıcıyı bir pool içine hapsederek, domain kullanıcısını kısıtlamış oluyorsunuz.

Centos 6.4 sistemde anlatacak olursak,

PHP-FPM servisinde default pool conf dosyası www.conf şeklinde geçiyor. Biz sitelerimiz için anımsatıcı bir conf dosyası kullanacağız.

```sh
#cd /etc/php-fpm.d/
#cp www.conf domain.conf
#vi domain.conf
```

domain.conf dosyasını php-fpm.conf içerisine ek olarak tanımlamamıza gerek yok. php-fpm.conf içerinde default olarak php-fpm.d içerisindeki dosyalar include edilmiştir.

domain.conf içeriğini aşağıdaki şekilde düzenliyoruz.

```sh
[domain.com]
listen = 127.0.0.1:9001
listen.allowed_clients = 127.0.0.1
user = gokhan
group = gokhan
pm = dynamic
pm.max_children = 9
pm.start_servers = 3
pm.min_spare_servers = 2
pm.max_spare_servers = 4
chdir = /
php_admin_value[open_basedir] = /var/www/html/domain.com:/usr/share/php5:/tmp:/usr/share/phpmyadmin:/etc/phpmyadmin:/var/lib/phpmyadmin
php_admin_value[disable_functions] = dl,exec,passthru,shell_exec,system,proc_open,popen,curl_exec,curl_multi_exec,parse_ini_file,show_source
```

user kısmını gokhan olarak belirledik. Eğer gokhan diye bir kullanıcı tanımlı değilse, php-fpm’i restart ettiğinizde kullanıcı idsini alamıyorum şeklinde bir hata ile karşılaşırsınız. Bu yüzden kullanıcıyı oluşturmamız gerekiyor.

```sh
#adduser -M -s /sbin/nologin gokhan
```

Yaptığımız  değişikliklerdeki yerlere  değinecek olursak,

```sh
listen = 127.0.0.1:9001
```

Listen satırı  her pool için farklı portta çalışacak şekilde ayarlanmalı.  Kullanılan bir port tanımlamanız durumunda php-fpm’i restart ettiğinizde aşağıdaki şekilde hata alabilirsiniz.

```sh
ERROR: [pool wwww] unable to set listen address as it's already used in another pool 'domain'
```

Owner olarak tanımlayacağınız kullanıcı ve grup ismi,

```sh
user = gokhan
group = gokhan
```

Process yöneticisinin child processleri nasıl kontrol edildiğinin tanımlandığı bölüm,

```sh
# dynamic yada static seçilebilir. Default olarak dynamic tanımlıdır.
pm = dynamic
#Aynı anda kaç tane child processin çalışabileceği değer.
#512 Mb ramli bir sunucuda 220 Mb Php-FPM için kullanmayı düşünürsek,
#her process için 24Mb ayırsak, 220 / 24 = 9.17 
pm.max_children = 9
#değer : min_spare_servers + (max_spare_servers - min_spare_servers) / 2
#olarak hesaplanır. Startup anında ne kadar child processin çalışacağını
#belirtinr 
pm.start_servers = 3
#min idle processi belirtir.
pm.min_spare_servers = 2
#max idle processi belirtir.
pm.max_spare_servers = 4
#Her child process için maks. request değeri. Default değer limitsiz şeklindedir.
pm.max_requests = 200
chdir = /
php_admin_value[open_basedir] = /var/www/html/domain.com:/usr/share/php5:/tmp:/usr/share/phpmyadmin:/etc/phpmyadmin:/var/lib/phpmyadmin
php_admin_value[disable_functions] = dl,exec,passthru,shell_exec,system,proc_open,popen,curl_exec,curl_multi_exec,parse_ini_file,show_source
```

Open_basedir ve çalışmasını istemediğiniz php fonksiyonlarını conf dosyasına aşağıdaki şekilde yazarak tanımlayabilirsiniz. Open_basedir oldukça önemli, php shell yada benzeri bir zararlı script yüklenmesi durumunda, home dizini dışırısına çıkmasını bu şekilde engellemiş oluyorsunuz.

```sh
php_admin_value[open_basedir] = /var/www/html/domain.com:/usr/share/php5:/tmp:/usr/share/phpmyadmin:/etc/phpmyadmin:/var/lib/phpmyadmin
php_admin_value[disable_functions] = dl,exec,passthru,shell_exec,system,proc_open,popen,curl_exec,curl_multi_exec,parse_ini_file,show_source
```

{{% notice note %}}
Burada ki open_basedir path’i, örnek olması açısından bu şekilde verilmiştir. Sunucudaki sitenin yapısına göre düzenlemeniz gerekebilir.
{{% /notice %}}

{{% notice note %}}
Mutlaka php’yi safe_mod açık  olaracak şekilde ayarlayın.
{{% /notice %}}

Conf dosyamızı düzenledikten sonra php-fpm’i restart edelim.

```sh
#/etc/init.d/php-fpm restart
```

Sıra geldi nginx domain.conf dosyasını düzenlemeye. Nginx’e domainleri daha düzenli göründüğü için conf.d dosyasından include ederek kullanıyorum. Bu yüzden domain için nginx conf dosyası domain.conf şekilde geçmekte.

PHP-FPM domain.conf dosyasını düzenlerken listen portunu 9001 yapmıştık. Nginx domain.conf dosyasında düzenleyelim.

```sh
#vi /usr/local/nginx/conf.d/domain.conf
```

fastcgi_pass satırını aşağıdaki şekilde düzenliyoruz.

```sh
location ~ \.php$ {
    include        fastcgi_params;
    fastcgi_pass   127.0.0.1:9001;
    fastcgi_index  index.php;
    fastcgi_param  SCRIPT_FILENAME  $request_filename;
```

Conf dosyasını düzenledikten sonra nginx’i restart etmeden önce nginx’in conf ayarlarında bir hata varmı diye kontrol etmemiz gerekiyor. Tüm sitelerin down olmasının önüne geçmiş olursunuz.

```sh
#/usr/local/nginx/sbin/nginx -t
```

Ok sonucunu aldıktan sonra nginx’i restart edebiliriz.

```sh
#/usr/local/nginx/sbin/nginx -s reload
```

TCP Bağlantısı yerine socket bağlantısı kullanıyor isek;

Php-fpm.d altında bulunan domain.conf dosyamızda tcp bağlantısı olarak ayarladığımız listen satırını, soket bağlantı olarak aşağıdaki şekilde ayarlayıp,

```sh
listen = /var/run/php5-fpm/domain.com.sock
```

daha sonra nginx domain.conf dosyamızda da fastcgi_pass satırını aşağıdaki gibi ayarlamalıyız.

```sh
listen = /var/run/php5-fpm/domain.com.sock
```

Artık servisleri restart edebilirsiniz.

```sh
#/etc/init.d/php-fpm restart
#/usr/local/nginx/sbin/nginx -t
#/usr/local/nginx/sbin/nginx -s reload
```

Kaynaklar:

http://bit.ly/16ottn9
http://bit.ly/17SgJZN
http://bit.ly/15jb23W
