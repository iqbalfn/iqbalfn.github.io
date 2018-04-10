---
layout: post
title: Instalasi PHP Secara Manual Dengan Perintah Configure
date: 2018-04-02 01:03:00
categories: notes server nginx security ssl
---

Instalasi PHP sebenarnya jauh lebih mudah jika menggunakan software installer seperti `yum` atau `apt-get`. Tapi ada kalanya penggunaan software installer ini menghalangi kita untuk mengkonfigurasi lebih dalam instalasi php. Untuk kondisi-kondisi tertentu mungkin instalasi dengan source lebih mudah karena memberi kebebasan mengatur konfigurasi sendiri. Di bawah ini adalah step-step yang saya gunakan untuk instalasi php secara manual dengan perintah configure, dan make.

## Devendencies

Beberapa software yang perlu terinstall di mesin sebelum melanjutkan proses instalasi adalah sebagai berikut:

1. make
1. gcc-c++
1. gcc
1. gcc-cpp
1. mysql-devel
1. pgsql-devel
1. lib64exif-devel
1. lib64zip-devel
1. lib64curl-devel
1. lib64gd-devel
1. lib64jpeg-devel
1. lib64mcrypt-devel
1. lib64openssl-devel
1. lib64readline-devel
1. lib64zlib-devel
1. lib64webp-devel
1. lib64xml2-devel
1. apache atau nginx

Instalasi software di atas lebih mudah jika menggunakan software installer.

## Download dan Extract PHP

Selanjutnya adalah men-download PHP, saya biasa menggunakan `wget`, lanjutkan dengan extract dengan perintah `tar` dan masuk ke dalam folder yang baru diextract.

{% highlight bash %}
wget http://id1.php.net/get/php-7.2.4.tar.bz2/from/this/mirror -O php-7.2.4.tar.bz2
tar jxf php-7.2.4.tar.bz2
cd php-7.2.4
{% endhighlight %}

## Instalasi

Selanjutnya adalah konfigurasi dengan perintah `configure`, dan install dengan perintah `make`:

{% highlight bash %}
./configure \
    --prefix=/usr/local/php \
    --enable-bcmath \
    --enable-exif \
    --enable-fpm \
    --enable-ftp \
    --enable-mbstring \
    --enable-mysqlnd \
    --enable-shmop \
    --enable-sockets \
    --enable-sysvmsg \
    --enable-sysvshm \
    --enable-sysvsem \
    --enable-zip \
    --with-apxs2=/usr/local/apache/bin/apxs \
    --with-curl \
    --with-fpm-user=iqbal \
    --with-fpm-group=iqbal \
    --with-gd \
    --with-jpeg-dir \
    --with-mcrypt \
    --with-mysqli \
    --with-openssl \
    --with-pear \
    --with-pdo-mysql \
    --with-pdo-pgsql \
    --with-pgsql \
    --with-readline \
    --with-zlib \
    --with-webp-dir
make
sudo make install
{% endhighlight %}

## Konfigurasi Akhir

Salin file `php.ini-development` ke instalasi php yang akan digunakan oleh php.

{% highlight bash %}
sudo cp php.ini-development /usr/local/php/lib/php.ini
{% endhighlight %}

Selanjutnya, jika menggunakan apache, maka tambahkan baris berikut pada file `/usr/local/apache/conf/httpd.conf`.

{% highlight apache %}
<FilesMatch \.php$>
   SetHandler application/x-httpd-php
</FilesMatch>
{% endhighlight %}