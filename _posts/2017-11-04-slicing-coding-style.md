---
layout: post
title: Slicing Coding Style
date: 2017-11-04 11:36:00
categories: notes design formal
---

Coding style di sini tidak selalu bisa diimplementasikan pada semua organisasi dan projek. Ini adalah dasar-dasar coding style yang mungkin bisa diikuti untuk mempermudah proses coding antara developer dan slicer ( designer ).

## Gunakan Double Quote pada Atribut Elemen

Selalu gunakan double quote ( `"` ) untuk atribut element. Perhatikan jika mengambil font dari google font, secara default mereka menggunakan single quote, ganti menjadi double quote:

{% highlight html %}
<!-- jangan -->
<a href='/'>Home</a>

<!-- gunakan -->
<a href="/">Home</a>
{% endhighlight %}

## Hilangkan Atribut type pada Tag script dan link

Pada tag `link` untuk `stylesheet` dan `script` untuk javascript, tidak perlu menambahkan atribut `type` yang berisi `text/javascript` atau `text/css`. Ini adalah urusan HTTP Header untuk menset content type, dan lagi sebagian besar browser tidak peduli dengan atribut ini.

{% highlight html %}
<!-- jangan -->
<link rel="stylesheet" href="/static/css/style.css" type="text/css">
<script src="/static/js/portal.js" type="application/javascript"></script>

<!-- gunakan -->
<link rel="stylesheet" href="/static/css/style.css">
<script src="/static/js/portal.js"></script>
{% endhighlight %}

## Tempatkan atribut rel, name, dan property di awal pada elemen link dan meta

Jika berurusan dengan tag `meta`, atau `link`, selalu awali atribut-nya dengan `rel`, `name`, atau `property` yang kemudian diikuti dengan `href` atau `content`.

{% highlight html %}
<!-- jangan -->
<link href="/static/css/style.css" rel="stylesheet">
<meta content="Lorem Ipsum Sit Dolor Amet" name="description">
<meta content="Website" property="og:title">

<!-- gunakan -->
<link rel="stylesheet" href="/static/css/style.css">
<meta name="description" content="Lorem Ipsum Sit Dolor Amet">
<meta property="og:title" content="Website">
{% endhighlight %}

## Gunakan HTML5

Tinggalkan HTML < 5. Mulai sekarang selalu gunakan html5. Awali setiap dokumen dengan `<!DOCTYPE html>`. Karena menggunakan HTML5, selalu tambahkan tag `<meta charset="utf8">` tepat di bawah tag `head`.

{% highlight html %}
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
</head>
<body>
</body>
<html>
{% endhighlight %}

## Jangan Gunakan self-closing-tag

Jika menggunakan tag `meta`, `link`, `img`, atau `br`, atau tag lainnya yang berdiri sendiri. Hilangkan karakter garis miring di akhir nama tag ( self-closing-tag ).

{% highlight html %}
<!-- jangan -->
<link rel="stylesheet" href="/static/css/style.css" />
<meta property="og:title" content="Website" />
<img src="/media/aa/bb/cc/media.jpg" alt="#" />
<br />

<!-- gunakan -->
<link rel="stylesheet" href="/static/css/style.css">
<meta property="og:title" content="Website">
<img src="/media/aa/bb/cc/media.jpg" alt="#">
<br>
{% endhighlight %}

## Indentasi Dimulai pada Anak head dan body

Tag `head` dan `body` tidak perlu memiliki indentasi. Tambahkan indentasi hanya pada anak tag mereka:

{% highlight html %}
<!-- jangan -->
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <h1>Hello World</h1>
    </body>
</head>

<!-- gunakan -->
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
</head>
<body>
    <h1>Hello World</h1>
</body>
</html>
{% endhighlight %}

## Simpan Semua Aset di dalam Folder Berdasarkan Type

Gabungkan semua aset pada masing-masing folder sesuai tipe file nya. Pada umumnya, semua css disimpan di folder `css`, dan javascript di folder `js`, begitu juga dengan font dan gambar. Tujuan utama dari pemisahan ini adalah mempermudah proses minify sebelum di-deploy ke server produksi.

Jika ada aset-aset yang dibutuhkan hanya pada saat development, seperti gambar-gambar, atau css pembatu, maka simpan semuanya di folder terpisah. Hal ini lebih mudah untuk menghindari masuknya mereka ke server produksi, atau repository.

Pastikan mengubah referensi file jika memindahkan file aset ke folder-nya masing-masing. Referensi file bisa ada di HTML, atau file css yang mereferensikan ke file gambar atau file css lainnya.

## Pritify HTML

Sebelum menyerahkan dokumen ke developer, pastikan semua HTML sudah di-pritify. Gunakan [dirtymarkup](http://dirtymarkup.com) untuk mempermudah proses pritify HTML.

## Jangan Minify css dan javascript

Tidak perlu me-minify aset css dan javascript, ada node ( grunt/gulp ) yang akan mengerjakannya untuk Anda begitu projek siap diunggah ke server produksi. Tidak perlu juga mengikutsertakan versi minify pada saat dokumen dikirim ke developer.

## Tambahkan css dan meta di head dan javascript di body

Semua file css dan tag `meta` harus ditambahkan di bagian `head`, sementara javascript harus dibagian akhir tag `body`. Hilangkan css di `body` dan tag `script` di head.

{% highlight html %}
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta property="og:title" content="Title"> <!-- gunakan
    <link rel="stylesheet" href="css/style.css"> <!-- gunakan -->
    <script src="js/jquery.min.js"></script> <!-- jangan -->
</head>
<body>
    <script src="js/portal.js"></script> <-- gunakan -->
    <link rel="stylesheet" href="css/style.css"> <!-- jangan -->
    <meta property="og:image" content="http://..."> <!-- jangan -->
</body>
</html>
{% endhighlight %}

## Jangan Ada Inline css/javascript

Hilangkan semua inline css dan javascript. Pindahkan semuanya ke dalam satu file dan include di dokumen.

{% highlight html %}
<!-- jangan -->
<style>.selector{ font-size: 12px }</style>
<script>function myAlert(text){ alert(text); }</script>

<!-- gunakan -->
<link rel="stylesheet" href="/static/css/custom.css">
<script src="/static/js/custom.js"></script>
{% endhighlight %}

## Jangan Gunakan Atribut style

Jangan pernah gunakan atribut `style`. Jika ada style yang hanya digunakan oleh satu elemen, pindahkan style tersebut ke file, dan include di html.

{% highlight html %}
<!-- jangan -->
<p style="font-size:12px">...</p>

<!-- gunakan -->
<link rel="stylesheet" href="/static/css/custom.css">
<p class="custom-p">...</p>
{% endhighlight %}

Contoh isi file `custom.css`:

{% highlight css %}
/** custom.css */
.custom-p{
    font-size: 12px;
}
{% endhighlight %}

## Indentasi

Gunakan 4 karakter spasi untuk indentasi. Sifat karakter tabulasi terlalu sulit untuk diprediksi, dan mungkin menghasilkan indentasi yang tidak konsisten di masing-masing text editor/IDE developer/designer. Sementara 4 karakter spasi akan selalu sama di semua text editor dengan tipe font monospace.

## Parent Masing-Masing Bagian Dokumen

Sebisa mungkin gunakan satu parent untuk semua bagian dokumen. Seperti header, konten utama, sidebar, dan footer agar memiliki parent element yang sama. Parent elemen bisa tag body, atau bisa juga tag div. Tujuan utama pemecahan seperti ini adalah agar masing-masing bagian bisa disimpan di file-file yang berbeda sehingga meminimalisir pengulangan penulisan script yang sama untuk masing-masing halaman.

Contoh yang boleh digunakan, tapi tidak disarankan:

{% highlight html %}
<body>
    <div class="container">
        <!-- HEADER START -->
        <header class="row">
            <div class="col-md-12">
                <!-- header content -->
            </div>
        </header>
        <!-- HEADER END -->
       
        <!-- MAIN CONTENT START -->
        <article class="row">
            <div class="col-md-8">
                <!-- main content -->
            </div>
            <!-- SIDEBAR CONTENT START -->
            <aside class="col-md-4">
                <!-- sidebar content -->
            </aside>
            <!-- SIDEBAR CONTENT END -->
        </article>
        <!-- MAIN CONTENT END -->
    </div>
   
    <!-- FOOTER START -->
    <footer>
        <!-- footer content -->
    </footer>
    <!-- FOOTER END -->
</body>
{% endhighlight %}

Lihat bahwa tag `footer` memiliki parent tag `body`, sementara tag `header`, dan `article` memiliki parent tag `div.container`, dan tag `aside` dengan parent tag `article`. Jika memungkinkan, dan sebagian besar kasus memang mungkin, gunakan struktur seperti di bawah:

{% highlight html %}
<body>
    <!-- HEADER START -->
    <header class="container">
        <div class="row">
            <!-- header content -->
        </div>
    </header>
    <!-- HEADER END -->
   
    <!-- MAIN CONTENT START -->
    <article class="container">
        <div class="row">
            <!-- main content -->
        </div>
    </article>
    <!-- MAIN CONTENT END -->
   
    <!-- SIDEBAR START -->
    <aside class="container">
        <!-- sidebar content -->
    </aside>
    <!-- SIDEBAR END -->
   
    <!-- FOOTER START -->
    <footer>
        <!-- footer content -->
    </footer>
    <!-- FOOTER END -->
</body>
{% endhighlight %}

Lihat bahwa semua bagian dokumen sekarang memiliki parent yang sama yaitu tag `body`. Dengan struktur seperti di atas, dokumen lebih mudah di pecah menjadi beberapa file.

## Harus Ada dan Hanya Ada Satu Tag H1 Dalam Satu Dokumen

Setiap dokumen pada masing-masing halaman projek, harus memiliki satu dan hanya satu tag `H1`. Cek lagi masing-masing halaman dan pastikan ada tag `H1` di dalamnya, dan hanya ada satu.

## Penggunaan H2 dan H3

Jika pada halaman ada subjudul yang berisi artikel, atau teks, atau daftar link ke halaman lain, pastikan subjudul tersebut adalah `H2`. Contoh penggunakan `H2` yang benar adalah pada widget Recent Post, atau Trending Post. Judul dari widget tersebut harus menggunakan tag `H2`.

Jika di dalam konten subjudul ada daftar link ke halaman lain di mana teks yang digunakan adalah judul dari halaman lain tersebut, gunakan tag `H3` untuk daftar judulnya.

## Buatkan File package.json

Pada awalnya, file ini digunakan oleh nodejs untuk identitas projek. Tapi sebaiknya gunakan juga file ini pada top folder design. File ini berisi informasi tentang tema, dan informasi pembuatnya. Buatkan satu file dengan perintah `npm init` jika sudah memiliki nodejs, atau buatkan file `package.json` secara manual dengan isi sebagai berikut:

{% highlight json %}
{
  "name": "[NAMA_TEMPLATE]",
  "version": "[VERSI]",
  "description": "[DESKRIPSI_SINGKAT]",
  "author": "[NAMA_DESIGNER] <[EMAIL_DESIGNER]> ([WEBSITE_DESIGNER])",
  "license": "[LISENSI]"
}
{% endhighlight %}

## Jangan Gunakan input untuk Tombol

Gunakan tag `input` hanya untuk menerima input dari user, sementara elemen `button` untuk menerima action dari user. Secara default, elemen button bertugas men-submit `form`. Jika hanya ingin menampilkan tombol untuk tujuan selain submit form, selalu tambahkan atribut `type="button"`.

{% highlight html %}
<!-- jangan -->
<form>
    <input type="submit" value="Save">
</form>

<!-- gunakan -->
<form>
    <button>Save</button>
</form>
{% endhighlight %}