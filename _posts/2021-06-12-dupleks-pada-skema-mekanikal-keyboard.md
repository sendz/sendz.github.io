---
layout: post
title: Desain Mechanical Keyboard dengan Duplex
type: post
date: 2021-06-12 15:16:00 +07:00
published: true
categories: qmk, mechanical keyboard
tags: kicad, qmk, mechanical keyboard
author: Sendy
comments: true
---

## Pendahuluan

Sebelum saya mulai pembahasan ini, saya anggap anda sekalian sudah memahami dasar-dasar membuat atau mendesain PCB mechanical keyboard dan cara membuat firmware di QMK. Jika belum, silakan kunjungi tautan berikut:
- [ruiqimao/keyboard-pcb-guide: Guide on how to design keyboard PCBs with KiCad](https://github.com/ruiqimao/keyboard-pcb-guide)
- [PCB Design | Keyboard Designer Wiki @ ai03.me](https://wiki.ai03.com/books/pcb-design)
- [QMK Firmware](https://docs.qmk.fm/#/)

## Pembahasan

### Keyboard Matrix

Keyboard matrix adalah sebuah matriks yang digunakan oleh mechanical keyboard untuk mendeteksi koordinat input yang dimasukkan oleh user dan diproses oleh MCU pada mekanikal keyboard. Misal pengguna menekan tombol di matriks row0 dan column1, maka output yang dihasilkan adalah angka 1 (pada kasus keyboard 60%).

Contoh matriks keyboard sederhana:

|          | **col0** | **col1** | **col2** | **dst** |
|----------|----------|----------|----------|---------|
| **row0** |   Esc    |    1     |    2     |   dst   |
| **row1** |   Tab    |    Q     |    W     |   dst   |

Pada umumnya, 1 pin di MCU akan digunakan untuk 1 *column* atau 1 *row*, sehingga jumlah pin yang digunakan akan sama dengan jumlah *column* dan jumlah *row* yang digunakan. Misal, untuk 1 buah keyboard 60% dibutuhkan 14 pin untuk *column* dan 5 pin untuk *row*, maka pin yang dibutuhkan adalah 19 buah pin. Untuk keyboard kecil dengan MCU ProMicro dengan fitur hanya keyboard mungkin tidak jadi masalah, tapi bagaimana kalau ingin menambah fitur seperti rotary encoder yang membutuhkan 2 pin tambahan? atau ingin merancang keyboard dengan matrix yang banyak tapi pin di MCU terbatas? Salah satu caranya adalah dengan *Duplex Matrix*

Apa itu duplex matrix? Yaitu sebuah cara untuk memaksimalkan penggunaan matriks atau grid dengan mengalikan 2 jumlah row, dan 1/2 jumlah column. Dari kasus keyboard 60% tadi, dengan 14 column dan 5 row, bisa dibuat duplex dengan 7 column dan 10 row, sehingga hanya membutuhkan 17 pin dibandingkan 19 pin, lebih hemat 2 pin. Tapi apakah semua kasus bisa diselesaikan atau bisa diefisienkan dengan duplex? Tentu tidak, macropad akan sama saja borosnya antara duplex dan matrix biasa.

Contoh matriks keyboard dengan dupleks:
|          | **col0** | **col0** | **col1** | **col1** | **dst** |
|----------|----------|----------|----------|----------|---------|
| **row0** |   Esc    |          |    2     |          |   dst   |
| **row1** |          |    1     |          |    3     |   dst   |
| **row2** |   Tab    |          |    W     |          |   dst   |
| **row3** |          |    Q     |          |    E     |   dst   |

![Schematic Grid]({{ site.baseurl }}/assets/keyboard-grids.png)
**Gambar di atas saya ambil dari [Matrices and Duplex Matrix | Keyboard Designer Wiki @ ai03.me](https://wiki.ai03.com/books/pcb-design/page/matrices-and-duplex-matrix)**

#### Kesimpulan

Kesimpulan dari Duplex Matrix adalah:
- 1 **column** di schematic digunakan untuk 2 **column** fisik di keyboard
- 2 **row** di schematic digunakan untuk 1 **row** fisik di keyboard

Kelebihan dari Duplex Matrix:
- Hemat pin
- Dari pin yang dihemat tadi bisa dibuat fitur tambahan seperti OLED screen, rotary encoder, RGB underglow, atau backlight.

Kekurangan dari Duplex Matrix:
- Wiring yang lebih kompleks
- Schematic yang lebih sulit
- Pengembangan firmware yang juga akan tambah sulit

### Pengembangan QMK Firmware

#### Masih Bahas Hardware

Bagaimana untuk pengembangan firmware nya? Apakah lebih sulit? Tentunya iya, dibandingkan dengan matriks tradisional. Di sini saya akan menggunakan kasus dari PCB yang saya desain [75 Pixels](https://github.com/sendyyeah/75-pixels) dengan schematic keys sebagai berikut

![75 Pixels Schematic]({{ site.baseurl }}/assets/75pixels-sch.png)

Dengan schematic tersebut, saya menggunakan 10 pin untuk row dan 8 pin untuk column, dan menghasilkan desain keyboard dengan 5 baris x 15 kolom, jadilah 75 buah tombol untuk digunakan, bahkan bisa ditambahkan hingga 80 buah tombol dengan menambah 1 kolom fisik lagi, dan desain tersebut saya gunakan ProMicro sebagai MCU-nya (jumlah pin yang bisa digunakan sebanyak 18 pin) tanpa harus hack atau [upgrade ProMicro](https://golem.hu/guide/pro-micro-upgrade/). Bayangkan jika menggunakan matriks tradisional, untuk 75 buah tombol dengan grid 5 baris x 15 kolom, saya membutuhkan 20 pin.

#### Lanjut Bahas Firmware

> Pada tahap ini, saya anggap semua pembaca sudah pernah atau sudah paham dasar-dasar menulis QMK firmware untuk sebuah keyboard, jadi saya cukup jelaskan bagaimana menulis kode untuk duplex matrix.

Pertama-tama yang harus dilakukan adalah mendefinisikan jumlah ROW dan COL di file `config.h`.

```CPP
#define MATRIX_ROWS 10
#define MATRIX_COLS 8
```

Jumlah `MATRIX_ROWS` dan `MATRIX_COLS` merepresentasikan jumlah pin yang digunakan, bukan jumlah dari *row* dan *column* secara fisik.

Masih di file `config.h`, kemudian menuju pin assignment, di sini dituliskan pin mana saja yang digunakan untuk ROW dan COL, penulisan diurutkan dari ROW0, ROW1, ROW2 dan seterusnya, begitu juga untuk *column*. Pastikan jumlah PIN yang dimasukkan sama dengan angka yang sudah dimasukkan pada tahap sebelumnya.

```CPP
#define MATRIX_ROW_PINS { B6, F4, F5, F6, F7, B1, B3, B2, B4, B5 }
#define MATRIX_COL_PINS { D3, D2, D1, D0, D4, C6, D7, E6 }
```

Selanjutnya adalah tahap yang paling membingungkan, mapping dari *schematic grid* / *schematic matrix* ke *keyboard layout* / *physical layout*. Buka file `{nama_keyboard}.h`, misal saya buka file `75pixels.h` dan definiskan sebuah layout. Karena `75 Pixels` adalah keyboard dengan layout Ortholinear, maka saya definiskan `LAYOUT_ortho_5x15`, jika yang sedang dikembangkan adalah keyboard biasa atau staggered, cukup definiskan dengan nama `LAYOUT`.

Ini adalah penjelasan singkat ketika mendefiniskan layout

```CPP
#define LAYOUT_ortho_5x15 ( __PHYSICAL_LAYOUT__ ) { __SCHEMATIC_GRID__ }
```

Ini versi sedikit lebih membingungkan

```CPP
#define LAYOUT_ortho_5x15 (
    GRID_0_0, GRID_1_0, GRID_0_1, GRID_1_1, GRID_0_2, GRID_1_2, GRID_0_3, GRID_1_3, ... \
    GRID_2_0, GRID_3_0, GRID_2_1, GRID_3_1, GRID_2_2, GRID_3_2, GRID_2_3, GRID_3_3, ... \
) {\
    {GRID_0_0, GRID_0_1, GRID_0_2, GRID_0_3, ...} \
    {GRID_1_0, GRID_1_1, GRID_1_2, GRID_1_3, ...} \
    {GRID_2_0, GRID_2_1, GRID_2_2, GRID_2_3, ...} \
    {GRID_3_0, GRID_3_1, GRID_3_2, GRID_3_3, ...} \
}
```

Sudah mulai bingung? Sama, saya juga dulu bingung. Jadi sederhananya adalah, matriks yang ada di bagian object `__SCHEMATIC_GRID__` akan diterjemahkan atau diuraikan menjadi `__PHYSICAL_LAYOUT`, karena apa yang kita lihat di schematic seperti ini

![Phyicial Grid]({{ site.baseurl }}/assets/physical-grid.png)

adalah

![Schematic Grid]({{ site.baseurl }}/assets/schematic-grid.png)

Selanjutnya? Silakan lanjutkan menambahkan layout di file `keymaps/default/keymap.c`, saya yakin kalau udah pernah bikin QMK firmware di sini sudah pasti paham caranya.

Terima kasih, selamat mencoba.