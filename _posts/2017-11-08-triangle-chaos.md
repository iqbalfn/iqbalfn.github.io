---
layout: post
title: Triangle Chaos
date: 2017-11-08 12:00:00
categories: js test logika
---

<style>
#container{position:relative;width:100%;padding-bottom:56.25%;border:1px solid #ccc;margin-bottom:25px;}
#drawer{position:absolute;left:50%;top:50%}
#dot-total-container{border:1px solid #ccc;font-size:11px;position:absolute;top:10px;right:10px;padding:0 10px;}
</style>
<div id="container">
	<canvas id="drawer"></canvas>
	<div id="dot-total-container">Dots total: <span id="dot-total">3</span></div>
</div>

Beberapa tahun yang lalu, ketika saya masih bekerja sebagai junior programer di BFI Bandung. Sore atau malam lah waktu itu ketika nongkrong di salah satu kafe berbarengan dengan tim yang lain. Kebetulan sekali duduk samping mas Hokky Situngkir yang menjabat sebagai pemimpin perusahaan. Entah kemana pembicaraan hingga akhirnya beliau menyinggung tentang suatu formula yang entah apa namanya. Penjelasannya cukup singkat, dan sangat mudah dimengerti, bahkan saya masih ingat betul cara dia menjelaskan. Coba cari-cari di google akhirnya tahu kalau formula itu adalah triangle chaos.

Karena penasaran ingin coba, akhirnya mulai membuka teks editor KWrite. Satu persatu stuktur html terselesaikan. Tak lama mulai masuk ke bagian javascript, tidak terlalu panjang, dan contoh implementasi triangle chaos pun selesai. Silahkan lihat demo di atas untuk tahu bagaimana hasil berkala ketika menjalankan formula triangle chaos terus menerus.

Di bagian atas kanan terdapat total dot yang terbuat. Saya menambahkan RGB agar terlihat lebih cantik, dimana bagian atas adalah merah, kanan bawah biru, dan kiri bawah hijau, semakin dekat posisi dot ke sisi segitiga, semakin jelas warnanya.

<script>
document.addEventListener('DOMContentLoaded', function(){
	var cont   = document.querySelector('#container');
    var height = cont.offsetHeight;
    var width  = cont.offsetWidth;
    
    var canvas = document.querySelector('#drawer');
    var ctx    = canvas.getContext('2d');
    
    var dtotal = document.querySelector('#dot-total');
    
    var size = (height > width ? width : height) - 50;
    var sizeHalf = size / 2;
    
    canvas.setAttribute('width', size);
    canvas.setAttribute('height', size);
    canvas.style.marginLeft = (0 - sizeHalf) + 'px';
    canvas.style.marginTop  = (0 - sizeHalf) + 'px';
    
    var taHeight = Math.sqrt( Math.pow(size, 2) - Math.pow(sizeHalf, 2) );
    
    var points = {
            1: {
                x: sizeHalf+1,
                y: Math.floor( ( size - taHeight ) / 2)
            },
            2: {
                x: 1,
                y: taHeight
            },
            3: {
                x: size-1,
                y: taHeight
            }
        };
    
    ctx.fillStyle = 'rgb(255,0,0)';
    ctx.fillRect(points[1].x, points[1].y, 1, 1);
    
    ctx.fillStyle = 'rgb(0,255,0)';
    ctx.fillRect(points[2].x, points[2].y, 1, 1);
    
    ctx.fillStyle = 'rgb(0,0,255)';
    ctx.fillRect(points[3].x, points[3].y, 1, 1);
    
    var dotTotal = 3;
    var lX = points[1].x;
    var lY = points[1].y;
    
    var draw = function(){
        dotTotal++;
        dtotal.innerHTML = dotTotal.toLocaleString();
        var dotTarget =  points[Math.floor((Math.random() * 3) + 1)];
        lX = Math.floor((dotTarget.x + lX) / 2);
        lY = Math.floor((dotTarget.y + lY) / 2);
        
        var red   = Math.floor(Math.sqrt(Math.pow(Math.abs(lX-points[1].x), 2)+Math.pow(lY-points[1].y, 2)));
        red = Math.floor(255 - ( 255 * red / size ));
        var green = Math.floor(Math.sqrt(Math.pow(lX-1,2) + Math.pow(points[2].y-lY,2)));
        green = Math.floor(255 - ( 255 * green / size ));
        var blue  = Math.floor(Math.sqrt(Math.pow(points[3].x-lX,2)+Math.pow(points[3].y-lY,2)));
        blue = Math.floor(255 - ( 255 * blue / size ));
        
        ctx.fillStyle = 'rgb('+red+','+green+','+blue+')';
        ctx.fillRect(lX, lY, 1, 1);
    }
    
    setInterval(draw, 10);
});
</script>