---
layout: post
title: Konfigurasi SSLForFree di Nginx Secara Manual
date: 2018-01-31 07:46:00
categories: notes server nginx security ssl
---

Cara yang paling benar dan yang paling direkomendasikan adalah metode yang mengautomatisasi proses pendaftaran dan renew certificate. Proses instalasi manual seperti ini sepertinya terlalu membutuhkan perhatian khusus karena certificate yang didaftarkan di sslforfree akan expired 90 hari kemudian, dan Anda sebagai developer harus memperbaharui certificate tersebut. Tapi jika Anda memang perlu, maka cara di bawah ini bisa diikuti dalam proses instalasi certificate sslforfree di engine nginx secara manual.

- Buka web sslforfree.com, dan daftarkan web Anda.
- Ikuti instruksinya, dan dapatkan file verifikasi yang diberikan.
- Simpan file tersebut di server, dan verifikasi web Anda.
- Download semua file certificate.
- Jalankan perintah berikut untuk menggabungkan file `certificate.crt` dan `ca_bundle.crt` menjadi `bundle.crt`

{% highlight bash %}
awk 1 certificate.crt ca_bundle.crt > bundle.crt
{% endhighlight %}

- Jalankan perintah berikut untuk menghasilkan file `privkey.pem` dari file `private.key`

{% highlight bash %}
openssl rsa -in private.key -text > privkey.pem
{% endhighlight %}

- Upload semuanya ke server, dan tempatkan di area yang tidak bisa diakses dari browser, dan bisa dibaca oleh user nginx.
- Tambahkan konfigurasi berikut pada konfigurasi web Anda di dalam seksi server:

{% highlight nginx %}
ssl on;
ssl_certificate /yourpath/bundle.crt;
ssl_certificate_key /yourpath/privkey.pem;
{% endhighlight %}

- Restart nginx. Enjoy!