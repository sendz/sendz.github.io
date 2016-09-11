---
layout: post
title: Tinggalkan Saja Twitter Bootstrap-nya
date: 2016-09-11 08:19:20 +07:00
type: post
published: true
categories: [webdev,bootstrap]
author: Sendy
comments: true
---

![Twitter Bootstrap]({{ site.baseurl }}/assets/bootstrap-site.png)

Judulnya dibikin gitu, biar rame, dan pos ini saya buat untuk mencoba integrasi jekyll blog saya dengan IFTTT. Saya adalah tipe orang yang gampang bosan dan suka mencoba hal baru, ketika sedang mencoba untuk membuat sebuah halaman web, saya selalu memanfaatkan sumber daya bantuan yang ada di internet, contohnya CSS Framework. CSS Framework mungkin adalah pilihan yang baik untuk mengawali sebuah desain tampilan dan membuat prototype (saya lebih senang membuat prototype langsung dengan file HTML tanpa alat pengolah gambar).
 
Kasus saya saat ini adalah, saya ingin membuat halaman web yang responsif, elemen yang bersahabat dengan perangkat mobile, ada beberapa tampilan yang menggunakan animasi parallax dan menu navigasi menggunakan off-canvas ketika dibuka di perangkat mobile. Lalu pertimbangan untuk memilih CSS Framework yaitu:

* Harus memiliki apapun yang saya butuhkan, sehingga tidak memerlukan plugin pihak ketiga
* Harus mudah dipelajari dan diimplementasi
* Memiliki dokumentasi yang baik

Dari hal di atas, saya mempertimbangkan untuk mencoba

* [Twitter Bootstrap](http://getbootstrap.com/)
* [uikit](http://getuikit.com/)
* [Semantic UI](http://semantic-ui.com/)
* [Materialize](http://materializecss.com/)

Setelah membaca masing-masing framework secara singkat, akhirnya saya memutuskan untuk menggunakan uikit, dengan beberapa pertimbangan, termasuk fitur-fitur bawaan yang dimiliki dan plugin javascript yang tersedia dalam paketnya. Saya buatkan tabel perbandingan agar lebih mudah dipahami kenapa saya memilih uikit.

| Nama Framework / Ulasan | Twitter Bootstrap | uikit | Semantic UI | Materialize |
|---|---|---|---|---|
| **Grid System** | Terdokumentasi dengan baik, sangat mudah dipahami, penamaan class dengan angka dimensi sangat mudah dipahami oleh awam | Dokumentasi yang rapih, sempat membuat saya bingung dengan penamaan class nya, tapi saya kira ini adalah penamaan class yang masuk akal dengan pendekatan pembagi, misal `.uk-width-1-3` adalah class untuk lebar 1/3 dari lebar keseluruhan | Memiliki dua pendekatan, lebar blok bisa ditentukan pada elemen tersebut, atau ditentukan di blok parent jika ingin membagi rata, sebelum memulai, pastikan dulu memahami filosofi grid system dari Semantic UI agar tidak tersesat | Sama seperti Twitter Bootstrap, hanya berbeda penamaan class saja|
| **Tampilan Dasar** | Dimensi dari tiap elemen dasar tampilan sangat pas ketika dimuat di perangkat mobile tanpa harus mengubah atau menambah class yang berkaitan dengan ukuran, sisanya sama saja dengan lainnya | Ukuran form terlalu kecil ketika dimuat di perangkat bergerak, saya harus menambah class `.uk-form-large` agar pas dengan jempol, sisanya sama dengan framework lain | Tampilan dasar yang sangat melimpah dan setiap elemen memiliki ukuran yang proporsional ketika dicoba di layar destop atau perangkat bergerak | Terinspirasi dari Material Design milik google, framework ini memiliki hampir semua desain material pada perangkat android dengan ukuran elemen yang sangat proporsional |
| **Fitur yang Dibutuhkan** | Tidak memiliki navigasi off-canvas untuk perangkat mobile, tidak tersedia animasi parallax dalam paket bawaan | Apa yang saya butuhkan ada di sini, memiliki bonus berupa switcher di form password | Semua yang saya butuhkan tersedia, ini adalah CSS Framework yang sangat lengkap | Parallax, off-canvas, semua ada |
| **Dokumentasi** | Dokumentasi sangat baik dan mudah dipahami | Dokumentasi sangat baik dan mudah dipahami, hanya saja dokumentasi dibagi menjadi dua bagian, yaitu Core untuk elemen inti dan Components untuk elemen tambahan atau plug-in bawaan | Dokumentasi lengkap dengan contoh kasus | Dokumentasi sangat baik dan ringkas |
| **Kesimpulan dan Kenapa** | Saya tidak memilih Twitter Bootstrap karena beberapa elemen yang saya butuhkan tidak tersedia, untuk sementara saya tidak akan menggunakan Twitter Bootstrap, faktor lain adalah framework ini terlalu sering digunakan banyak orang :D | Walaupun saya tidak yakin dengan popularitas framework ini, tapi sepertinya Yoothemes sebagai pencetus memiliki keseriusan membangun framework ini, dengan ketersediaan elemen yang saya butuhkan beserta plug-in bawaan yang cukup melimpah, saya langsung jatuh hati. Alasan lain adalah karena membawa icon font-awesome sebagai paket bawan | Semantic UI sangat lengkap dan sangat serius, saya sempat heran kenapa CSS Framework memiliki fitur Behaviors termasuk API handler sendiri, saya rasa ini UI Framework | Saya sangat suka dengan desain material karena lebih rapih dibanding desain tampilan mobile yang lain, tapi karena pada dasarnya saya ingin membuat tampilan web secara umum, bukan 'mobile web/app', saya tinggalkan dulu Materialize ini |

Sepertinya saya akan mulai menggunakan uikit ini untuk beberapa waktu kedepan dan meninggalkan sementara Twitter Bootstrap yang biasa saya pakai untuk membuat halaman web atau sekedar coba-coba. Maaf jika tidak tersedia gambar atau tangkapan layar, karena saya menulis ini sambil menahan perut mulas.

Selamat bereksperimen.