---
layout: post
title: ES6 Untuk Manusia
date: 2017-11-04 12:58:00
categories: notes js formal
---

Artikel ini adalah translasi langsung dari artikel [Deepak Grover](//github.com/metagrover/ES6-for-humans).

## 1. let, const dan skop blok

`let` memungkinkan Anda membuat deklarasi yang hanya berlaku dalam satu blok, disebut skop blok. Berbeda dengan menggunakan `var` yang berlaku pada skop fungsi, sangat disarankan menggunakan `let` di ES6.

{% highlight javascript %}
var a = 2;
{
    let a = 3;
    console.log(a); // 3
    let a = 5; // TypeError: Identifier 'a' has already been declared
}
console.log(a); // 2
{% endhighlight %}

Satu lagi pendeklarasian skop blok adalah dengan menggunakan `const`, yang membuat konstan. Di ES6, const mewakili konstan referensi pada nilai. Dengan kata lain, nilai tidak terkunci, hanya penempatan ulang yang terkunci. Ini contoh sederhananya:

{% highlight javascript %}
{
    const B = 5;
    B = 10; // TypeError: Assignment to constant variable

    const ARR = [5, 6];
    ARR.push(7);
    console.log(ARR); // [5,6,7]
    ARR = 10; // TypeError: Assignment to constant variable
    ARR[0] = 3; // ini bisa berubah
    console.log(ARR); // [3,6,7]
}
{% endhighlight %}

Yang perlu diingat:

1. Penarikan `let` dan `const` berbeda dengan penarikan variabel dan fungsi. Keduanya `let` dan `const` bisa ditarik, tapi tidak bisa diakses sebelum dideklarasikan, karena Temporal [Dead Zone](http://jsrocks.org/2015/01/temporal-dead-zone-tdz-demystified/)
1. `let` dan `const` hanya berlaku pada blok yang paling dekat dengannya.
1. Ketika menggunakan `const`, gunakan CAPITAL_CASING (ini kebiasaan umum).
1. `const` harus didefinisikan dengan pendeklarasiannya.

## 2. Fungsi Tanda Panah

Fungsi tanda panah adalah cara singkat penulisan fungsi di ES6. Pendefinisian fungsi tanda panah terdiri dari daftar parameter ( `...` ), diikuti dengan tanda `=>` dan isi fungsi.

{% highlight javascript %}
// Expresi fungsi baisa
let addition = function(a, b) {
    return a + b;
};

// Implementasi dengan fungsi tanda panah
let addition = (a, b) => a + b;
{% endhighlight %}

Sebagai catatan pada contoh di atas, penggunaan fungsi tanda panah menggunakan "isi yang ringkas" yang tidak membutuhkan statemen `return` yang jelas.

Ini contoh dengan "blok isi" yang biasa

{% highlight javascript %}
let arr = ['apple', 'banana', 'orange'];

let breakfast = arr.map(fruit => {
    return fruit + 's';
});

console.log(breakfast); // ['apples', 'bananas', 'oranges']
{% endhighlight %}

**Tunggu! Masih ada lagi...**

Fungsi tanda panah tidak hanya membuat fungsi jadi lebih pendek. Tapi juga pada prilaku `this` di dalamnya.

Fungsi tanda panah dengan keyword `this` berbeda dengan fungsi normal. Masing-masing fungsi pada JavaScript memiliki `this`-nya masing-masing tapi fungsi tanda panah mengambil `this` dari blok yang paling dekat dengannya. Coba lihat kode di bawah:

{% highlight javascript %}
function Person() {
    // constructor Person() mendefinisikan `this` sebagai instannya sendiri.
    this.age = 0;

    setInterval(function growUp() {
        // Di mode non-strict, fungsi growUp() mendefinisikan `this`
        // sebagai objek global, yang berbeda dengan `this`
        // yang didefinisikan oleh constructor Person().
        this.age++;
    }, 1000);
}
var p = new Person();
{% endhighlight %}

Di ECMAScript 3/5, masalah seperti ini diperbaiki dengan menggunakan `this` dari yang paling dekat.

{% highlight javascript %}
function Person() {
    var self = this;
    self.age = 0;

    setInterval(function growUp() {
        // Callback menggunakan variabel `self` yang
        // nilainya adalah objek yang diharapkan.
        self.age++;
    }, 1000);
}
{% endhighlight %}

Seperti dijelaskan sebelumnya, fungsi tanda panah mengambil this dari blok yang paling dekat dengannya, jadi kode di bawah akan berjalan seperti yang diharapkan, bahkan dengan fungsi tanda panah yang bersarang.

{% highlight javascript %}
function Person() {
    this.age = 0;

    setInterval(() => {
        setTimeout(() => {
            this.age++; // `this` diambil dari objek Person
        }, 1000);
    }, 1000);
}

var p = new Person();
{% endhighlight %}

Baca lebih lanjut tentang 'Lexical this' di fungsi tanda panah [di sini](//developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions#Lexical_this)

## 3. Nilai Standar Parameter Fungsi

ES6 memungkinkan Anda untuk menset nilai standar parameter fungsi. Ini ilustrasi simpel nya.

{% highlight javascript %}
let getFinalPrice = (price, tax = 0.7) => price + price * tax;
getFinalPrice(500); // 850
{% endhighlight %}

## 4. Operator Spread / Rest

Operator `...` disebut sebagai operator spread atau rest, bergantung pada di mana dan kapan dia digunakan.

Ketika digunakan pada sesuatu yang bisa diurai, dia bertindak sebagai "pemecah/spread" menjadi elemen individu:

{% highlight javascript %}
## function foo(x, y, z) {
    console.log(x, y, z);
}

let arr = [1, 2, 3];
foo(...arr); // 1 2 3
{% endhighlight %}

Penggunaan umum lainnya dari `...` adalah menyatukan nilai-nilai menjadi array. Di sini dia bertugas sebagai operator "rest".

{% highlight javascript %}
function foo(...args) {
    console.log(args);
}
foo(1, 2, 3, 4, 5); // [1, 2, 3, 4, 5]
{% endhighlight %}

## 5. Ekstensi Objek Literal

ES6 memungkinkan pendeklarasian objek dengan syntax tulis cepat untuk inisialisasi properti dari variabel dan mendefinisikan fungsi method. Juga memungkinkan kalkulasi untuk penamaan properti.

{% highlight javascript %}
function getCar(make, model, value) {
    return {
        // dengan nilai properti syntax tulis cepat
        // Anda bisa menghilankan nilai properti itu
        // jika namanya sama dengan nama variabel yang ada
        make,  // sama dengan make: make
        model, // sama dengan model: model
        value, // sama dengan value: value

        // kalkulasi penamaan properti sekarang
        // didukung
        ['make' + make]: true,

        // pendefinisian method dengan syntax tulis cepat
        // bisa menghilangkan keyword `function` dan titik dua
        depreciate() {
            this.value -= 2500;
        }
    };
}

let car = getCar('Kia', 'Sorento', 40000);
console.log(car);
// {
//     make: 'Kia',
//     model:'Sorento',
//     value: 40000,
//     makeKia: true,
//     depreciate: function()
// }
{% endhighlight %}

## 6. Literal Octal dan Binary

ES6 sekarang mendukung literal oktal dan binari. Awali angka dengan `0o` atau `0O` akan mengkonversi nilainya menjadi oktal. Lihat kode di bawah:

{% highlight javascript %}
let oValue = 0o10;
console.log(oValue); // 8

let bValue = 0b10; // 0b atau 0B untuk binari
console.log(bValue); // 2
{% endhighlight %}

## 7. Destructur Array dan Object

Destructur membantu menghilangkan kebutuhan variabel sementara ketika bekerja dengan objek dan array.

{% highlight javascript %}
function foo() {
    return [1, 2, 3];
}
let arr = foo(); // [1,2,3]

let [a, b, c] = foo();
console.log(a, b, c); // 1 2 3

function bar() {
    return {
        x: 4,
        y: 5,
        z: 6
    };
}
let { x: a, y: b, z: c } = bar();
console.log(a, b, c); // 4 5 6
{% endhighlight %}

## 8. super di Objek

ES6 memungkinkan menggunakan method `super` di objek dengan prototipe. Ini adalah contoh sederhananya:

{% highlight javascript %}
var parent = {
    foo() {
        console.log("Hello from the Parent");
    }
}

var child = {
    foo() {
        super.foo();
        console.log("Hello from the Child");
    }
}

Object.setPrototypeOf(child, parent);
child.foo(); // Hello from the Parent
             // Hello from the Child
{% endhighlight %}

## 9. Templet Literal and Pembatas

ES6 memperkenalkan cara lebih mudah menambahkan interpolasi yang dievaluasi otomatis.

1. `${ ... }` digunakan untuk render variabel.
1. <code>`</code> Backtick digunakan untuk pemisah.

{% highlight javascript %}
let user = 'Kevin';
console.log(`Hi ${user}!`); // Hi Kevin!
{% endhighlight %}

## 10. for...of vs for...in

`for...of` mengurai nilai objek yang bisa diurai, seperti array.

{% highlight javascript %}
let nicknames = ['di', 'boo', 'punkeye'];
nicknames.size = 3;
for (let nickname of nicknames) {
    console.log(nickname);
}
// di
// boo
// punkeye
{% endhighlight %}

`for...in` mengurai properti dari objek.

{% highlight javascript %}
let nicknames = ['di', 'boo', 'punkeye'];
nicknames.size = 3;
for (let nickname in nicknames) {
    console.log(nickname);
}
// 0
// 1
// 2
// size
{% endhighlight %}

## 11. Map dan WeakMap

ES6 memperkenalkan data struktur baru bernama `Map` dan `WeakMap`. Kita sebenarnya menggunakan map di JavaScript selama ini. Faktanya semua objek bisa dianggap adalah `Map`.

Sebuah objek terdiri dari key ( selalu string ) dan nilai, sedangkan di `Map`, semua nilai (objek maupun nilai primitif) bisa digunakan sebagai key atau nilai. Coba lihat kode di bawah:

{% highlight javascript %}
var myMap = new Map();

var keyString = "a string",
    keyObj = {},
    keyFunc = function() {};

// set nilai
myMap.set(keyString, "value associated with 'a string'");
myMap.set(keyObj, "value associated with keyObj");
myMap.set(keyFunc, "value associated with keyFunc");

myMap.size; // 3

// getting the values
myMap.get(keyString);    // "value associated with 'a string'"
myMap.get(keyObj);       // "value associated with keyObj"
myMap.get(keyFunc);      // "value associated with keyFunc"
{% endhighlight %}

### WeakMap

`WeakMap` adalah `Map` yang mana key nya direferensikan dengan lemah, yang tidak menghalangi key nya dari garbage-collected. Maksutnya Anda tidak perlu khawatir tentang memory leaks.

Satu lagi yang perlu diketahui di sini, di `WeakMap` sebagai kebalikan dari `Map` *semua key harus objek*.

`WeakMap` hanya punya empat method `delete(key)`, `has(key)`, `get(key)` dan `set(key, value)`.

{% highlight javascript %}
let w = new WeakMap();
w.set('a', 'b');
// Uncaught TypeError: Invalid value used as weak map key

var o1 = {},
    o2 = function(){},
    o3 = window;

w.set(o1, 37);
w.set(o2, "azerty");
w.set(o3, undefined);

w.get(o3); // undefined, karena nilai ini yang di set

w.has(o1); // true
w.delete(o1);
w.has(o1); // false
{% endhighlight %}

## 12. Set dan WeakSet

objek `Set` adalah koleksi nilai unik. Nilai duplikat tidak dianggap, karena semua anggota koleksi harus unik. Nilainya bisa tipe primitif atau referensi objek.

{% highlight javascript %}
let mySet = new Set([1, 1, 2, 2, 3, 3]);
mySet.size; // 3
mySet.has(1); // true
mySet.add('strings');
mySet.add({ a: 1, b:2 });
{% endhighlight %}

Anda bisa mengurainya sesuai dengan urutan ditambahkan nilainya dengan method `forEach` atau pengulangan `for...of`.

{% highlight javascript %}
mySet.forEach((item) => {
    console.log(item);
    // 1
    // 2
    // 3
    // 'strings'
    // Object { a: 1, b: 2 }
});

for (let value of mySet) {
    console.log(value);
    // 1
    // 2
    // 3
    // 'strings'
    // Object { a: 1, b: 2 }
}
{% endhighlight %}

Set juga memiliki method `delete()` dan `clear()`.

### WeakSet

Hampir sama dengan `WeakMap`, objek `WeakSet` memungkinkan Anda menyimpan objek di koleksi dengan referensi lemah. Sebuah objek di `WeakSet` terjadi hanya sekali; hal ini unik di koleksi `WeakSet`.

{% highlight javascript %}
var ws = new WeakSet();
var obj = {};
var foo = {};

ws.add(window);
ws.add(obj);

ws.has(window); // true
ws.has(foo);    // false, foo belum ditambahkan ke dalam koleksi

ws.delete(window); // hapus window dari set
ws.has(window);    // false, window sudah dihapus
{% endhighlight %}

## 13. Class di ES6

ES6 memperkenalkan syntax class baru. Satu hal yang perlu diketahui di sini adalah class ES6 bukan model object-oriented inheritance baru. Ini hanya membuat syntax yang lebih baik dari inheritance berdasarkan protoype JavaScript.

Cara tepat melihat class di ES6 adalah syntax baru untuk bekerja dengan prototype dan fungsi constructor yang sudah sering kita gunakan di ES5.

Fungsi yang didefinisikan dengan keyword `static` mengimplementasikan fungsi static/class di dalam class.

{% highlight javascript %}
class Task {
    constructor() {
        console.log("task instantiated!");
    }
   
    showId() {
        console.log(23);
    }
   
    static loadAll() {
        console.log("Loading all tasks..");
    }
}

console.log(typeof Task); // function
let task = new Task(); // "task instantiated!"
task.showId(); // 23
Task.loadAll(); // "Loading all tasks.."
{% endhighlight %}

`extends` dan `super` di class

Lihat kode di bawah:

{% highlight javascript %}
class Car {
    constructor() {
        console.log("Creating a new car");
    }
}

class Porsche extends Car {
    constructor() {
        super();
        console.log("Creating Porsche");
    }
}

let c = new Porsche();
// Creating a new car
// Creating Porsche
{% endhighlight %}

`extends` memungkinkan child class untuk inherit dari parent class di ES6. Sangat penting bahwa konstruktor pembawa harus memanggil `super()`.

Selain itu, Anda juga bisa memanggil method dari parent class dengan menggunakan `super.parentMethodName()`.

Lebih lengkap tentang class di sini

Beberapa hal perlu diingat:

Pendeklarasian class tidak ditarik. Anda perlu mendeklarasikan class untuk mengaksesnya, atau error ReferenceError akan terjadi. Tidak perlu menggunakan keyword `function` ketika mendefinisikan fungsi di dalam class.

## 14. Symbol

`Symbol` adalah data tipe unik dan kekal yang diperkenalkan di ES6. Tujuan dibuatnya symbol adalah untuk membuat identifier unik tapi Anda tidak pernah bisa mengakses identifiernya.

Inilah bagaimana cara membaut symbol:

{% highlight javascript %}
var sym = Symbol("some optional description");
console.log(typeof sym); // symbol
{% endhighlight %}

Catatan bahwa Anda tidak bisa menggunakan new dengan `Symbol(...)`.

Jika `Symbol` digunakan sebagai property suatu objek, datanya disimpan dengan cara yang spesial yang mana propertinya tidak muncul dengan pencacahan properti objek biasa.

{% highlight javascript %}
var o = {
    val: 10,
    [Symbol("random")]: "I'm a symbol",
};

console.log(Object.getOwnPropertyNames(o)); // val
{% endhighlight %}

Untuk mendapatkan properti symbol objek, gunakan `Object.getOwnPropertySymbols(o)`

## 15. Iterasi

Iterator mengakses individu dari koleksi satu dalam satu waktu, sementara menyimpan informasi posisi dimana dia sekarang. Ada method `next()` yang mengembalikan individu selanjutnya dalam urutan. Method ini mengembalikan objek dengan dua properti: `done` dan `value`.

ES6 memiliki `Symbol.iterator` yang dikhususkan untuk iterator untuk objek. Tiapkali objek perlu diuraikan (seperti di awal pengulangan `for..of`), method @@iterator yang dipanggil tanpa argument, dan iterator yang dikembalikanlah yang digunakan untuk nilai yang akan diuraikan.

Coba lihat sebuah array, yang adalah terurai, dan iterator bisa mengambil nilainya:

{% highlight javascript %}
var arr = [11,12,13];
var itr = arr[Symbol.iterator]();

itr.next(); // { value: 11, done: false }
itr.next(); // { value: 12, done: false }
itr.next(); // { value: 13, done: false }

itr.next(); // { value: undefined, done: true }
{% endhighlight %}

Catatan bahwan Anda bisa membuat iterator buatan dengan mendefinisikan `obj[Symbol.iterator]()` dengan definisi objek.

## 16. Generator

Fungsi generator adalah fitur baru di ES6 yang memungkinkan fungsi menggenerasi beberapa nilai dengan mengembalikan sebuah objek yang bisa diuraikan untuk mengambil nilai dari fungsi itu sendiri satu nilai dalam satu waktu.

Fungsi generator mengembalikan sebuah iterable object ketika dipanggil. Ditulis dengan syntax baru `*` sama seperti keyword `yield` yang baru diperkenalkan di ES6.

{% highlight javascript %}
function *infiniteNumbers() {
    var n = 1;
    while (true) {
        yield n++;
    }
}

var numbers = infiniteNumbers(); // mengembalikan objek iterable

numbers.next(); // { value: 1, done: false }
numbers.next(); // { value: 2, done: false }
numbers.next(); // { value: 3, done: false }
{% endhighlight %}

Tiap kali yield di panggil, nilai dari penghasilan menjadi nilai selanjutnya di urutan.

Juga, catatan bahwa generator mengkalkulasi nilainya berdasarkan permintaan, yang memungkinkan representasi urutan yang efisin pada perhitungan yang sibuk, atau bahkan urutan tak terbatas.

## 17. Promise

ES6 memiliki dukungan natif untuk promise. promise adalah objek yang menunggu operasi asynchronous sampai selesai, dan ketika operasi itu selesai, promise akan memenuhi permintaan atau menolaknya.

Cara membuat promise adalah dengan menggunakan constructor `new Promise()` yang menerima handler yang akan diberikan dua fungsi sebagai parameter fungsi. Parameter pertama (biasanya disebut resolve) adalah fungsi untuk dipanggil ketika siap; dan handler kedua (biasanya disebut reject) adalah fungsi yang untuk menolak Promise jika dia tidak bisa menghasilkan nilai yang diharapkan.

{% highlight javascript %}
var p = new Promise(function(resolve, reject) { 
    if (/* condition */) {
        resolve(/* value */);  // berhasil
    } else {
        reject(/* reason */);  // error, ditolak
    }
});
{% endhighlight %}

Setiap Promise punya method dengan nama `then` yang menyimpan `callback`. Callback pertama dipanggil jika promise berhasil ( resolved ), sementara yang kedua jika promise direject.

{% highlight javascript %}
p.then((val) => console.log("Promise Resolved", val),
       (err) => console.log("Promise Rejected", err));
{% endhighlight %}

Nilai yang dikembalikan dari callback `then` akan dikirimkan ke callback `then` selanjutnya.

{% highlight javascript %}
var hello = new Promise(function(resolve, reject) { 
    resolve("Hello");
});

hello.then((str) => `${str} World`)
     .then((str) => `${str}!`)
     .then((str) => console.log(str)) // Hello World!
{% endhighlight %}

Ketika mengembalikan promise, nilai yang resolved dari promise akan diteruskan ke callback selanjutnya dengan rantai yang efektif secara bersamaan. Ini adalah tehnik untuk menghindari "callback hell".

{% highlight javascript %}
var p = new Promise(function(resolve, reject) { 
    resolve(1);
});

var eventuallyAdd1 = (val) => {
    return new Promise(function(resolve, reject){
        resolve(val + 1);
    });
}

p.then(eventuallyAdd1)
 .then(eventuallyAdd1)
 .then((val) => console.log(val)) // 3
{% endhighlight %}