---
layout: post
title: Password Generator
date: 2017-11-05 17:59:00
categories: tools security
---

<pre id="result"> </pre>

Please jangan berharap ada sesuatu yang luar bisa di sini. Jangan berharap ada tools dimana kamu bisa konfigurasi agar menghasilkan random password yang terfilter. Tool ini cuma menggenerasi 10 random string dari koleksi printable karakter setiap kali halaman ini direfresh. Karakter-karakter yang digunakan adalah karakter acak dari:

```
abcdefghijklmnopqrstuvwxyz
ABCDEFGHIJKLMNOPQRSTUVWXYZ
0123456789
/*-+,<.>/?;:\'"[{]}\\|=_)(*&^%$#@!`
```

Lihat hasil nya, dan gunakan. Kalau merasa kurang cocok, coba refresh lagi halaman ini untuk menghasilkan password baru. Tenang saja, saya tidak menyimpan password yang tergenerasi, kalau tidak percaya, silah inspect element atau view source tools di atas.

 <script>
    var strs = 'abcdefghijklmnopqrstuvwxyz0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ`-=[]\\;\',./~!@#$%^&*()_+|}{:"?><';
    var res  = '';
    var el   = document.querySelector('#result');
    for(var i=0; i<10; i++)
        res+= strs[Math.floor((Math.random()*strs.length))];
    el.innerText = res;
</script>