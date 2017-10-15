---
layout: post
title: Tools yang Biasa Saya Gunakan Ketika Bekerja sebagai Web Frontend Developer
type: post
date: 2017-10-15 12:31:00 +07:00
published: true
categories: programming
tags: automation, tools, programming
author: Sendy
comments: true
---

Tujuan utama sebagai seorang programmer adalah menghasilkan produk, selain itu juga harus bisa menjaga kualitas produk dengan baik. Betul apa betul?

Jadi, saya akan sedikit berbagi beberapa alat yang bisa membantu kita untuk jadi frontend programmer yang baik dan keliatan lebih keren dari teman-teman yang belum tahu. Selain itu, kita bisa jadi lebih produktif dan lebih sering ngopi-ngopi hangat atau minum coklat hangat :joy:

### Bash

![Bash dengan zsh]({{ site.baseurl }}/assets/bash.png)

Bash, atau shell, atau terminal, atau console adalah alat yang paling utama yang harus dimiliki. Bash biasa ditemui di Sistem Operasi GNU/Linux, UNIX Like, OSX, untuk Sistem Operasi Windows bisa [dipasang sendiri](https://www.howtogeek.com/249966/how-to-install-and-use-the-linux-bash-shell-on-windows-10/).

Kenapa harus punya bash? Karena kebanyakan alat-alat untuk membantu pengembangan aplikasi lebih mudah dan lebih cepat ketika dijalankan melalu bash/terminal dibandingkan dengan menggunakan GUI.

### Git

![Git]({{ site.baseurl }}/assets/git-logo.png)

Git adalah alat untuk membantu kita mengatur versi dari kode sumber suatu program dan juga membantu saat kolaborasi dalam tim. Dengan bantuan git, kita bisa mengelola kode, sumber daya, dan member tim dengan lebih mudah (Jika sudah memahami dasar penggunaan git).

### Bower

Bower adalah manajemen paket (package manager) yang berisi berbagai kebutuhan library untuk pengembangan website seperti Twitter Bootstrap, jQuery, Angular, dll. Bower membutuhkan [`npm`](https://www.npmjs.com/) dan [`nodejs`](https://nodejs.org/en/).

### NPM

NPM adalah manajemen paket (package manager) untuk aplikasi berbasis JavaScript yang berjalan diatas runtime NodeJS.

### Gulp

![Git]({{ site.baseurl }}/assets/gulp-logo.jpg)

Gulp sendiri adalah sebuah alat untuk mengotomasi seluruh pekerjaan yang biasa kita lakukan berulang-ulang, seperti proses compile file sass ke css, minifikasi kode sumber, hingga deploy ke server.

Dengan menggunakan alat otomasi seperti gulp, kita tidak perlu lagi mengurusi hal-hal yang selalu dikerjakan berulang-ulang, dan membuat kita lebih produktif bahkan lebih sering nyeruput teh hangat di depan layar.

### Kode Editor

Saya tidak bisa menulis secara eksplisit nama kode editor yang digunakan, karena saya sendiri menggunakan beberapa kode editor untuk kebutuhan berbeda, dan karena kode editor merupakan mazhab seorang programmer. Jadi, silakan memilih kode editor sesuai kebutuhan masing-masing.

Kebutuhan saya untuk kode editor setidaknya memiliki:
- Built in terminal
- Ketersedian plugin atau ad-ons
- Code Hint
- Stabil

Dari syarat diatas, saya gunakan Visual Studio Code (Gratis) dan WebStorm dari Jetbrains (Berbayar)

### Live Reload

LiveReload adalah alat agar saya tidak perlu memuat ulang halaman web yang sedang saya kerjakan, karena server LiveReload sendirilah yang akan meminta browser untuk _refresh_ halaman tersebut untuk saya.

Untuk gulp, ada plugin untuk menjalankan livereload yang bernama [gulp-livereload](https://www.npmjs.com/package/gulp-livereload)

### Web Browser

Di mana saya bisa menjalankan aplikasi yang saya bangun kalau bukan di Web Browser? Saya menggukanan Google Chrome versi umum yang sudah stabil, karena ketersedian alat yang didukung oleh Google Chrome bisa digunakan oleh kita sebagai frontend developer.

Saya juga menguji aplikasi web di beberapa browser, diantaranya:
- Chromium (versi open source dari Google Chrome)
- Mozilla Firefox
- Safari
- Opera