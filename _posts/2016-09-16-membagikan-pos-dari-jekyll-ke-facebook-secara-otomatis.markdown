---
layout: post
title: Membagikan Post dari Blog Jekyll ke Facebook Secara Otomatis dengan IFTTT
date: 2016-09-16 23:06:00 +07:00
type: post
published: true
categories: [Jekyll]
author: Sendy
comments: true
---

Selama nulis di Jekyll, padahal baru dua kali yang asli di Jekyll karena sisanya pindahan dari WordPress, saya koneksikan Jekyll ini ke IFTTT biar bisa membagikan otomatis ke Facebook dan Twitter, _auto-share_ bahasa kerennya. Karena Jekyll ini berbeda dengan WordPress yang punya plugin Jetpack, Jekyll harus dibantu oleh IFTTT atau If This, Then That.

IFTTT sendiri adalah sebuah aplikasi yang memungkinkan kita melakukan _re-share_ otomatis ataupun _auto-share_ dari satu platform ke platform lainnya. Misal, kalau habis pos di Twitter, pos kita harus masuk juga di Facebook, bisa? Bisa. Bahkan sampai disuruh kirim SMS pun bisa, tapi yang ini belum saya coba.

Hal pertama yang harus dilakukan adalah mengetahui URL dari RSS Feed blog Jekyll kita, biasanya sih di `https://namadomain/feed.xml`, anggap aja begitu. Seperti punya saya ini di `https://sendz.github.io/feed.xml`. Selanjutnya, register IFTTT di [https://ifttt.com/](https://ifttt.com/), IFTTT ini juga punya aplikasi mobile untuk Android, coba cek Play Store :wink:

Sudah register? Sudah! Oke! Terus Login ya.

Lanjut klik navigasi **My Recipes** di atas ini (saya kotakin merah)

![IFTTT Home]({{ site.baseurl }}/assets/ifttt-home.png)

Lalu klik **New Recipes**

![IFTTT New Recipes]({{ site.baseurl }}/assets/ifttt-recipes.png)

Klik **this** yang berwarna biru, lalu cari **Feed** atau dengan nama **RSS**, selanjutnya klik lagi logo Feed tadi.

![IFTTT this]({{ site.baseurl }}/assets/iftttt-this.png)

Pilih yang **New Feed**

![IFTT New Feed]({{ site.baseurl }}/assets/ifttt-new-feed.png)

Langsung masukan URL feed Jekyll kita tadi, lalu klik **Create Trigger**

![IFTT Create Trigger]({{ site.baseurl }}/assets/ifttt-create-trigger.png)

Selanjutnya klik link **that** dan pilih action yang akan kita gunakan, di sini saya ambil contoh kasus Facebook pribadi. Kalau anda punya Facebook Page atau fans page, bisa gunakan yang itu.

![IFTT Actions]({{ site.baseurl }}/assets/ifttt-action.png)

Sampai di sini, silakan pilih yang mana, mau pilih **Create a new status message** yang sama seperti update status pada umumnya, atau **Create a link post** lengkap dengan format link yang detailnya bisa digenerate, atau **Upload a photo from URL** untuk menambahkan pos dengan preview photo.

![IFTT Action Facebook]({{ site.baseurl }}/assets/ifttt-facebook.png)

Sampai sini, terserah deh mau diisi apa, setelah itu jangan lupa klik **Create Action**

![IFTT Action Facebook Entry]({{ site.baseurl }}/assets/ifttt-facebook-entry.png)

Sampai sini, kita tinggal klik **Create Recipe** atau kasih nama sekalian biar gak salah edit.

![IFTT Give a Recipe Name]({{ site.baseurl }}/assets/ifttt-give-a-name.png)

Terakhir adalah, coba pos satu buah konten melalui Jekyll dan tunggu hasilnya :+1:

Kalau resep kita berhasil, akan ada pemberitahuan warna hijau