---
title: Mengenal Android MVP Pattern - Sebuah Konsep
layout: post
date: 2016-10-26 20:14:11 +07:00
type: post
published: true
categories: android
author: Sendy
comments: true
---

![YOU DA REAL MVP]({{ site.baseurl }}/assets/69y8SWx.jpg)

Sesekali nulis rada serius isinya, tentang Android MVP Pattern yang saya sendiri baru benar-benar paham konsepnya minggu ini. Biasanya, kita membuat aplikasi android dengan pattern MVC, yang bukan **Model View Controller** itu, tapi **Massively View Controller** yang artinya semua logic code dan business code disimpan di **View**.

## Sekilas tentang MVP

### Apa itu? apa MVP?

MVP atau **Model View Presenter** adalah sebuah konsep dimana kita memisahkan logic code atau business code dari **View** ke **Presenter**, jadi tugas utama dari **Presenter** adalah sebagai penyedia data dari **Model**, selain sebagai penyedia data, **Presenter** juga bisa menyimpan logic yang biasa kita simpan di **View** dan menerima action yang dikirim dari **View**. **Model** itu sendiri adalah sekumpulan dari `data structure` yang dikumpulkan menjadi satu class, tugasnya untuk menyimpan data dari API response atau menyimpan data untuk API request.

Jadi, gambarannya seperti ini

```
Model <-> Presenter <-> View
```

Secara garis besar, **Presenter** adalah penghubung antara **Model** dan **View**. Yang artinya, **View** tidak lagi berkomunikasi dengan **Model** karena **Presenter** yang akan menyajikan **Model** pada **View**

### Mengapa MVP?

Ini pertanyaan saya dulu, kenapa harus MVP? ~~Selain untuk gaya-gayaan atau keren-kerenan~~ Fungsi utama dari MVP adalah sebagai berikut:

* Beban kerja dari **View** lebih ringan karena tidak mengurusi `http request` atau `http response`.
* Membagi tugas ke setiap layer sehingga `code` menjadi **TESTABLE**.
* Membagi kode-kode yang bejibun menjadi bagian-bagian kecil dan sesederhana mungkin.
* Mempermudah `code maintenance`.

### Kapan harus menggunakan MVP?

Kapan-kapan bisa, tapi ketika fitur dan kode di aplikasi kita semakin bertambah, inilah saatnya untuk beralih. Biar kualitas dari aplikasi juga lebih meningkat, begitu.

## Contoh Kasus

Contohnya, saya punya aplikasi sederhana, yaitu aplikasi penghitung luas dari nilai `panjang` * `lebar` yang hanya akan memiliki 1 buah layout dan 1 buah kelas `Activity`, oke?

Kira-kira seperti ini kode yang biasa ditemui.

<script src="https://gist.github.com/sendz/1e5b578b81a23e754c80d2120e22ef89.js"></script>

### Refactor

Yang akan dilakukan selanjutnya adalah, memecah beberapa baris kode di MainActivity.java dan mengekstraknya menjadi `method` atau `function`. Istilahnya, kita akan melakukan **refactor** atau merubah kode menjadi lebih baik.

Sampai saat ini, aplikasi berjalan dengan baik.

![Aplikasi Hitung Luas]({{ site.baseurl }}/assets/hitung-luas.png)

Selanjutnya, kita akan membuat tiga buah file baru bernama `MainPresenter`, `MainView` dan `MainModel`, dimana `MainActivity` akan mengimplementasikan `MainView` dan juga mendeklarasikan `MainPresenter` di dalamnya, dan `MainModel` akan berisi variabel dari `luas`. Fungsi dari `MainView` sendiri adalah agar `MainPresenter` bisa berinteraksi dengan code yang ada di `MainActivity`.

Hal yang akan dilakukan adalah:

1. Membuat class baru bernama `MainPresenter`, `MainModel` dan interface baru bernama `MainView`;
2. Implementasikan `MainView` di `MainActivity`;
3. Deklarasikan `MainPresenter` di `MainActivity` dengan parameter `MainView`;
4. Pindahkan fungsi `hitungLuas(int panjang, int lebar)` ke `MainPresenter`;
5. Buat fungsi baru `void tampilkanLuas(int luas)` di `MainView`;
6. Tambahkan anotasi `@Override` di atas `tampilkanLuas(int luas)` di `MainActivity`;
5. Panggil `hitungLuas(panjang, lebar)` dari `MainPresenter` di `MainActivity`;
6. Panggil `tampilkanLuas(luas)` dari `MainActivity` melalui `MainView` di `MainPresenter`;

Pada contoh kasus ini, **View** akan memiliki *behavior* yang disebut `Tell, don't Ask` ke **Presenter** yang artinya **View** tidak akan menerima *returned value* dari **Presenter**, tetapi **Presenter** lah yang akan memberikan perintah untuk mengeksekusi fungsi yang ada di **View** beserta data yang dibutuhkan. Oleh karena itu, fungsi dalam `MainActivity` haruslah mengimplementasikan fungsi dari `MainView`.

Dan di akhir, code saat ini adalah:

<script src="https://gist.github.com/sendz/0d865b229b0f70f71dc51f96236a2864.js"></script>

### Unit Test

Salah satu kelebihan dari MVP adalah setiap logic code atau business code bisa diuji dengan `Unit Test`, pada kasus ini saya akan menggunakan `Junit4` sebagai library untuk melakukan pengetesan `Unit Test`.

Tambahkan terlebih dahulu `testCompile group: 'org.mockito', name: 'mockito-core', version: '2.2.3'` ke file `build.gradle` di dalam tag `dependencies` untuk menggunakan Mockito sebagai library pembantu. Mockito memiliki fungsi yang bisa digunakan untuk mereplikasi sebuah kelas dan bisa kita gunakan untuk pengujian pada level `Unit Test`. Setelah menambahkan baris tadi, refresh gradle dengan klik teks `Sync Now` pada bar yang muncul lalu klik menu **Build -> Rebuild Project**.

Buatlah sebuah file dengan nama `MainPresenterTest` di folder `test/java/`, atau **Klik Kanan -> Go To -> Test -> Create New Test...**, gunakan `Junit4` sebagai TestLibrary lalu pilih direktori `test`, bukan `androidTest`.

Berikut adalah file `Unit Test` yang saya buat dengan nama `MainPresenterTest`.

<script src="https://gist.github.com/sendz/b366d23534e482b4099483f82bf693dc.js"></script>

Sekarang, inilah struktur project kita saat ini

![Struktur Project]({{ site.baseurl }}/assets/Screen Shot 2016-10-29 at 00.06.38.png)

Dan ini hasil dari pengujian `Unit Test` pada logic code di `MainPresenter`.

![Hasil Unit Test]({{ site.baseurl }}/assets/unit-test-hitung-luas.png)

Dan ini aplikasi dengan tampilan dan hasil yang sama dengan sebelumnya

![Aplikasi]({{ site.baseurl }}/assets/Screen Shot 2016-10-28 at 23.59.35.png)

## Tautan Project di github

[Github Project](https://github.com/KSL-Kusuka/belajar-android/tree/mvp)

Sekian, selamat mencoba.