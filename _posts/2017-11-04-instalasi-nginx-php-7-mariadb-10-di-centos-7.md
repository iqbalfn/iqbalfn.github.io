---
layout: post
title: Instalasi Nginx, PHP 7, MariaDB 10 di CentOS 7
date: 2017-11-04 16:53:00
categories: server php tutorial mariadb nginx
---

Tutorial ini akan menjelaskan secara lengkap proses instalasi Nginx, PHP 7.0, MariaDB 10.1 di CentOS 7. Mengingat CentOS 7 belum meiliki official repository untuk php 7, dan mariadb 10, maka proses instalasi mungkin sedikit berbeda dengan `yum install app` saja.

## Persiapan

Silahkan konek ke server dengan ssh ( atau putty di windows ) dengan user `root`. Hal ini penting karena hampir semua proses membutuhkan akses root.

{% highlight bash %}
# Konek ke server
ssh root@192.168.30.63
{% endhighlight %}

### Server user

Hal pertama yang akan kita lakukan adalah membuatkan user untuk user nginx dan php. Saya lebih suka membuatkan user nginx sama dengan user php sama dengan user yang akan melakukan file upload ke server. Dengan begitu, kesalahan-kesalahan kecil seperti nginx/php tidak bisa menulis ke folder karena berbeda user bisa dihindari.

Untuk tujuan ini, saya akan membuatkan user untuk nginx, php, dan user yang akan meng-upload file ke server dengan nama `devel`.

{% highlight bash %}
# buat user baru
useradd devel

# buat password untuk user devel
passwd devel
{% endhighlight %}

Perintah di atas akan membuat user baru dengan nama `devel`, kemudian membuatkan password ke user tersebut dengan perintah `passwd devel`.

### Web folder

Dikarenakan user server yang akan kita gunakan adalah `devel`, maka kita perlu menyimpan semua data web di bawah folder home user devel. Buatkan folder `/home/devel/html` yang nantinya menjadi tempat semua website disimpan. Sebelum membuat folder, pastikan sudah login sebagai user devel dengan perintah `su - devel` agar folder yang terbuat adalah milik devel, dan bukan milik root.

{% highlight bash %}
# login sebagai user devel
su - devel

# buatkan folder /home/devel/html
mkdir /home/devel/html
{% endhighlight %}

Lanjutkan dengan membuatkan folder untuk web pertama kita, yaitu alamat IP server untuk tester instalasi nginx dan php.

{% highlight bash %}
# folder web ip pertama
mkdir /home/devel/html/192.168.30.63
{% endhighlight %}

Proses pembuatan web fodler selesai, keluar dari user devel agar Anda kembali menjadi root dengan perintah `exit`.

## Instalasi Nginx

Begitu folder-folder yang akan kita butuhkan nanti sudah dibuat, sekarang kita bisa lanjutkan proses instalasi nginx.

### Install epel-release

Anda sekarang login sebagai root, selanjutnya, install `epel-release` sebelum melanjutkan instalasi nginx.

{% highlight bash %}
# install epel-release
yum install epel-release
{% endhighlight %}

Tekan `y` jika ditanyakan `Is this ok [y/d/N]:`

### Install nginx

Setelah `epel-release` terpasang, lanjutkan dengan instalasi nginx.

{% highlight bash %}
# Install nginx
yum install nginx
{% endhighlight %}

Seperti langkah sebelumnya, pilih `y` jika ditanyakan `Is this ok [y/d/N]:`

### User nginx

Secara default, nginx terinstall dengan user `nginx`. Ganti user nginx dengan user devel sesuai dengan ketentuan awal kita tadi. Penggantiannya bisa dengan `nano`, atau `vim`. Saya pribadi lebih suka dan lebih mengerti menggunakan `vim`. Edit file `/etc/nginx/nginx.conf`, cari baris `user nginx;`, dan ganti menjadi `user devel;`.

{% highlight bash %}
# ganti user nginx menjadi devel
vim /etc/nginx/nginx.conf
{% endhighlight %}

{% highlight nginx %}
# /etc/nginx/nginx.conf
# user nginx;
user devel;
{% endhighlight %}

Begitu user nginx diganti, ketik `:wq` untuk keluar dari editor jika menggunakan `vim`.

### Test Instalasi

Jalankan nginx, dan coba akses alamat ip server dari browser. Jika Anda melihat halaman default nginx, berarti semuanya sudah berjalan dengan baik. Lanjutkan dengan menambahkan nginx ke system start up, agar nginx langsung dijalankan ketika server baru dinyalakan.

{% highlight bash %}
# jalankan nginx
systemctl start nginx

# tambahkan nginx ke system start up
systemctl enable nginx
{% endhighlight %}

### Test instalasi nginx

Jika Anda melihat error page dan tidak bisa mengakses web. Matikan sebentar firewall di server centos untuk melihat apakah firewall yang berbuat disini.

### Akses nginx

Karena pada saat dipasang, user nginx adalah nginx, maka folder preset nginx semuanya menjadi milik user nginx. Kita perlu mengubah beberapa folder agar tetap bisa digunakan oleh nginx walaupun user nginx sudah diganti menjadi devel.

Ganti owner dan group folder `/var/lib/nginx` menjadi milik devel. Folder ini menjadi tempate penyimpanan sementara nginx ketika user upload file.

Secara default, CentOS 7 terpasang `SELinux`. Oleh karena itu, kita juga perlu memperbolehkan SELinux agar user devel bisa beraktifitas di folder /home/devel/html yang sebelumnya kita buat.

{% highlight bash %}
# ganti owner dan group /var/lib/nginx
chown -R devel:devel /var/lib/nginx

# selinux di /home/devel/html
chcon -R -t httpd_sys_rw_content_t /home/devel/html
{% endhighlight %}

Proses instalasi nginx sudah selesai sampai di sini. Konfigurasi web pertama akan kita lakukan nanti begitu php terpasang.

## Instalasi MariaDB

Secara default, CentOS 7 membawa repo MaraiDB 5.x. Karena versi ini bisa terbilang cukup tua, maka kita akan mengambil instalasi dari repository MariaDB langsung.

### Persiapan

Buatkan file `/etc/yum.repos.d/MariaDB.repo`.

{% highlight bash %}
# buatkan file /etc/yum.repos.d/MariaDB.repo
vim /etc/yum.repos.d/MariaDB.repo
{% endhighlight %}

Kemudian tambahkan konten di bawah ini ke file tersebut:

{% highlight bash %}
# /etc/yum.repos.d/MariaDB.repo
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.1/rhel7-amd64
pgpkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1
{% endhighlight %}

Simpan dan tutup editor vim dengan perintah :wq.

### Instalasi

Kemudian lanjutkan dengan instalasi `mariadb` dan `mariadb-server`. Begitu mariadb terpasang, jalankan! Kemudian tambahkan mariadb ke system start up. Begitu semuanya selesai, jalankan mysql secure installation untuk menset password root, dan mempersiapkan server. Ikuti instruksi yang diberika ketika proses securing mariadb.

{% highlight bash %}
# install mariadb dan mariadb-server
yum install mariadb mariadb-server

# jalankan mariadb
systemctl start mariadb

# tambahkan mariadb ke start up
systemctl enable mariadb.service

# mengamankan mariadb
mysql_secure_installation
{% endhighlight %}

Beberapa pertanyaan yang akan ditanyakan adalah:

```
    Enter current password for root (enter for none):
    Tekan enter karena kita memang belum punya password untuk user root.
    Set root password? [Y/n]
    Tekan Y karena kita memang harus men-set password untuk user root. Lanjutkan dengan mengisi password untuk root.
    Remove anonymous users [Y/n]
    Tekan Y karena kita tidak ingin ada user anon yang mengakses server mariadb ini.
    Disabled root login remotly? [Y/n]
    Untuk ini, saya lebih terbiasa pilih Y. Karena memang belum pernah ada kebutuhan untuk akses server mysql dari luar server.
    Remove test database and access to it? [Y/n]
    Biasanya saya juga tidak pernah menggunakan database test, jadi di step ini, sekali lagi saya pilih Y.
    Reload privilege tables now? [Y/n]
    Tekan Y untuk ini.
```

### User MariaDB

Kita tidak akan menggunakan user root untuk aktifitas dengan database dari website. Kita akan membuatkan satu user untuk itu, user ini adalah user devel. Sama dengan user nginx sebelumnya. Silahkan login ke mariadb dan buatkan satu user dengan nama devel dan passwordnya.

{% highlight bash %}
# login ke mariadb
mysql -p
{% endhighlight %}

{% highlight sql %}
-- buat user devel di mariadb
CREATE USER 'devel'@'localhost' IDENTIFIED BY '123456';

-- grant akses ke user tersebut
GRANT ALL PRIVILEGES ON *.* TO 'devel'@'localhost';

-- keluar dari mariadb
exit;
{% endhighlight %}

Test user baru dengan login sebagai user tersebut, buatkan database baru, gunakan database tersebut, buatkan tabel, drop table, dan drop database.

{% highlight bash %}
# login sebagai user devel
mysql -p -udevel
{% endhighlight %}

{% highlight sql %}
-- buat database lorem
CREATE DATABASE `lorem`;

-- masuk ke database tersebut
USE `lorem`;

-- buat table tester
CREATE TABLE `lor` ( `id` INTEGER NOT NULL AUTO_INCREMENT PRIMARY KEY );

-- drop lagi tabel nya
DROP TABLE `lor`;

-- drop database nya
DROP DATABASE `lorem`;

-- keluar dari mariadb
exit;
{% endhighlight %}

## Instalasi PHP 7

Sama seperti MariaDB, CentOS 7 belum memiliki official repository untuk php 7, jadi kita akan menggunakan pihak ketiga untuk ini.

### Persiapan

Pastikan masih login sebagai root. Kembali ke home root dan jalankan perintah di bawah untuk mendownload repo installer:

{% highlight bash %}
# kembali ke home
cd ~

# download installer
curl 'https://setup.ius.io/' -o setup-ius.sh

# jalankan installer
bash setup-ius.sh
{% endhighlight %}

Perintah di atas akan mendownload installer repo, dan `ius-release`.

### Instalasi

Lanjutkan dengan instalasi php dengan library nya.

{% highlight bash %}
# instalasi php dan library
yum install php70u-fpm-nginx \
    php70u-cli \
    php70u-mysqlnd \
    php70u-gd \
    php70u-xml \
    php70u-json
{% endhighlight %}

### Konfigurasi

Begitu php terpasang, lanjutkan dengan konfigurasi php. Edit file `/etc/php-fpm.d/www.conf`, ubah nilai user dan group menjadi `devel` sesuai dengan nama user server yang kita buat sebelumnya. jadikan komen baris `listen = 127.0.0.1:9000` dan uncomment baris `listen = /run/php-fpm/www.sock` karena akan menggunakan socked daripada port. Selanjutnya cari baris `listen.acl_users = nginx` kemudian uncomment dan ubah nilai nya menjadi `devel`. Proses konfigurasi file `www.conf` selesai, simpan dan keluar dari editor.

Selanjutnya, kita akan mengkonfigurasi php-nginx, yaitu konfigurasi global nginx untuk php yang akan digunakan oleh semua website di server ini. Edit file `/etc/nginx/conf.d/php-fpm.conf`, lanjutkan dengan men-comment baris `server 127.0.0.1:9000` dan meng-uncomment baris `server unix:/run/php-fpm/www.sock`. Simpan dan tutup file ini. Urusan kita dengan nginx-php selesai di sini.

### Service PHP-FPM

Karena urusan kita dengan php selesai, lanjutkan dengan menjalankan service php-fpm dengan perintah di bawah:

{% highlight bash %}
systemctl start php-fpm
{% endhighlight %}

Jika tidak ada masalah, maka kita bisa lanjutkan dengan menambahkan php-fpm ke start up agar service php-fpm dijalankan ketika server dinyalakan ulang. Jalankan perintah berikut untuk menambahkan php-fpm ke system start up.

{% highlight bash %}
systemctl enable php-fpm
{% endhighlight %}

### Test Server

Semua sudah terpasang, selanjutnya, kita akan membuatkan satu webserver dengan hostname ip address server. Untuk itu, buatkan sebuah file konfigurasi nginx di `/etc/nginx/conf.d/192.168.30.63.conf`, sebaiknya ganti nilai 192.168.30.63 dengan ip server Anda. Edit file ini dan tambahkan konten seperti di bawah:

{% highlight nginx %}
server {
    listen 80;
    server_name 192.168.30.63;

    root /home/devel/html/192.168.30.63;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }

    error_page 404 /404.html;
    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        root /usr/share/nginx/html;
    }

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_pass php-fpm;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
{% endhighlight %}

Simpan dan keluar dari editor. Kemudian, buatkan file index.php (`/home/devel/html/192.168.30.63/index.php`) di root web pertama ini. Pastikan membuat file ini dengan user devel, jadi login dulu sebagai user devel dan lanjutkan proses pembuatan file. Isi dengan konten seperti di bawah:

{% highlight php %}
<?php

phpinfo();
{% endhighlight %}

Restart nginx, kemudian buka browser Anda dan akses alamat IP server.

Proses instalasi selesai di sini.