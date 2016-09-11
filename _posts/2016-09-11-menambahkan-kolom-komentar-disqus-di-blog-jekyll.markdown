---
title: Menambahkan Kolom Komentar Disqus di Blog Berbasis Jekyll
date: 2016-09-11 13:26:00 +07:00
layout: post
type: post
published: true
comments: true
categories: Jekyll
tags: [disqus, jekyll, comments, komentar]
author: Sendy
---

![Disqus]({{ site.baseurl }}/assets/disqus.png)

Mulai berpindah dari wordpress ke Jekyll yang dihost di github, karena gratis dan gak bakal pusing takut sama defacer lagi, lah kan repo blog nya udah diset private :smile:

Karena Jekyll ini static page, dan tidak memiliki fitur manajemen komentar, pilihannya adalah dengan menyisipkan disqus. Yang belum tau apa itu disqus, silakan akses link [Disqus](https://disqus.com/) dan buat channel baru di [Link ini](https://disqus.com/admin/create/).

Setelah urusan dengan disqus selesai, sekarang urusan dengan platform jekyll nya. Buka file `_layouts/post.html` lalu tambahkan kode berikut:

<script src="https://gist.github.com/sendz/f5998ec86f95c6d727a7b745a7de064f.js"></script>

Setelah dengan file `_layouts/post.html` selanjutnya file `_layouts/page.html`, akan sedikit berbeda, karena untuk `post`, secara default komentar akan muncul, dan untuk `page` box komentar tidak akan muncul jika variabel `comments` di front matter belum diset sebagai `true`.

<script src="https://gist.github.com/sendz/cbb909ffb542702318e842b9fbf427a6.js"></script>

Selanjutnya, untuk mengaktifkan atau menonaktifkan kolom komentar, di page setting bisa diatur dengan mengganti variabel `comments: true` atau `comments: false`.

Selamat mencoba :+1:.