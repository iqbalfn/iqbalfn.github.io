---
layout: post
title: Binary Clock
date: 2017-11-09 10:16:00
categories: js test logika
---

<style>
#main-container{position:relative;width:100%;height:200px;margin-bottom:25px;}
#container{position:absolute;font-size:0;top:50%;left:50%;width:270px;height:175px;margin-left:-135px;margin-top:-87.5px;}
.col{width:45px;height:100%;display:inline-block;vertical-align:top;text-align:center;}
.box{width:40px;height:40px;margin: 0 auto;margin-bottom:5px;}
.box:last-child{margin-bottom:0}
.dot{background-color:#eee;}
.on{background-color:#ccc;}
</style>

<div id="main-container">
	<div id="container">
	    <div class="col">
	        <div class="box"></div>
	        <div class="box"></div>
	        <div class="box dot" id="hour-20"></div>
	        <div class="box dot" id="hour-10"></div>
	    </div>
	    <div class="col">
	        <div class="box dot" id="hour-8"></div>
	        <div class="box dot" id="hour-4"></div>
	        <div class="box dot" id="hour-2"></div>
	        <div class="box dot" id="hour-1"></div>
	    </div>
	    <div class="col">
	        <div class="box"></div>
	        <div class="box dot" id="minute-40"></div>
	        <div class="box dot" id="minute-20"></div>
	        <div class="box dot" id="minute-10"></div>
	    </div>
	    <div class="col">
	        <div class="box dot" id="minute-8"></div>
	        <div class="box dot" id="minute-4"></div>
	        <div class="box dot" id="minute-2"></div>
	        <div class="box dot" id="minute-1"></div>
	    </div>
	    <div class="col">
	        <div class="box"></div>
	        <div class="box dot" id="second-40"></div>
	        <div class="box dot" id="second-20"></div>
	        <div class="box dot" id="second-10"></div>
	    </div>
	    <div class="col">
	        <div class="box dot" id="second-8"></div>
	        <div class="box dot" id="second-4"></div>
	        <div class="box dot" id="second-2"></div>
	        <div class="box dot" id="second-1"></div>
	    </div>
	</div>
</div>

Kalau kamu terbiasa baca jam dinding, dan atau jam digital di jam tangan atau handphone kamu, pastinya akan bingung bagaimana cara baca penunjuk jam berbentuk binary di atas. Tapi kalau kamu bisa baca, tentu jadi lebih menyenangkan. Bayangkan semua jam di rumah menggunakan binary clock, untuk tahu sekarang jam berapa saja harus ngitung matematika dulu.

Contoh jam binari di atas menggunakan javascript, silahkan inpect elemen kalau ingin tahu bagaimana cara pembuatannya. Sementara untuk tahu bagaimana cara bacanya, silahkan singgah di paman Google.

<script>
    var indexes = [1,2,4,8,10,20,40];
    var selectors = {
        hour  : { el: {}, js: 'getHours' },
        minute: { el: {}, js: 'getMinutes' },
        second: { el: {}, js: 'getSeconds' }
    };
    for(var k in selectors){
        for(var i=0; i<indexes.length; i++){
            var el = document.getElementById(k+'-'+indexes[i]);
            if(el)
                selectors[k].el[indexes[i]+''] = el;
        }
    }

    function getActiveIndex(val){
        if(val < 10)
            val = '0' + val;
        val+= '';
        var vals = val.split('');
        var results = [];

        for(var i=0; i<vals.length; i++){
            var suf = !i?'0':'';
            var val = parseInt(vals[i]);

            if(val >= 8)
                val-= results.push('8'+suf) && 8;
            if(val >= 4)
                val-= results.push('4'+suf) && 4;
            if(val >= 2)
                val-= results.push('2'+suf) && 2;
            if(val >= 1)
                val-= results.push('1'+suf) && 1;
        }

        return results;
    }

    setInterval(function(){
        var time = new Date();
        for(var k in selectors){
            var els = selectors[k].el;
            var js  = selectors[k].js;
            for(var l in els)
                els[l].classList.remove('on');
            var tt  = time[js]();
            var sls = getActiveIndex(tt);
            for(var i=0; i<sls.length; i++)
                els[sls[i]].classList.add('on');
        }
    }, 500);
</script>