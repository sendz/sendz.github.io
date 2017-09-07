---
layout: post
title: Seminggu Bersama TypeScript
type: post
date: 2017-09-07 16:13:00 +07:00
published: true
categories: programming
author: Sendy
comments: false
---

![TypeScript]({{ site.baseurl }}/assets/typescript.png)

Saya dulu koding JavaScript kemudian pindah ke Java, saat itu saya kangen dengan simple-nya JavaScript. Tahun ini, saya kembali ke JavaScript dan garap angularjs, framework tua. Yang terjadi adalah, saya rindu Java dengan data type yang dideklarasikan untuk setiap variable, kenapa? Karena kesederhanaan JavaScript sering membuat saya kebingungan apakah saya harus mengirim parameter dengan tipe data `string`, `integer`, `float` atau `object`?

Kemudian kami di kantor memutuskan untuk sedikit memindahkan beberapa basis kode ke tempat lain, berpindah ke format bahasa pemrograman, atau berpindah framework. Opsi saat itu adalah pindahkan keseluruhan kode ke Angular 4, atau ke ES6, atau ke TypeScript, atau ke ReactJS. Dari berbagai opsi, kami sepakat untuk memindahkan ke TypeScript karena tidak ingin menulis ulang keluruhan kode jika beralih framework. Kenapa tidak memilih ES6? Karena kami tidak bisa mendeklarasikan tipe data secara eksplisit untuk tiap variable yang kami buat.

### Tentang TypeScript

TypeScript adalah transpiler (saya tidak akan menyebutnya bahasa pemrograman) yang menerjemahkan format TypeScript ke JavaScript, baik ES5, ES6 atau ES2015. TypeScript dirancang dan dibuat oleh Microsoft, tapi jangan takut harus bayar biaya lisensi, karena TypeScript itu gratis dan Open Source dengan lisensi [Apache License 2.0](https://en.wikipedia.org/wiki/Apache_License).

Seperti namanya, TypeScript berarti ~~script yang masih harus diketik~~ script yang mengenalkan pendekatan `type`, pokoknya gitu lah.

### TypeScript Basic Types

TypeScript memiliki tipe data dasar, diantaranya

- boolean
- number
- string
- array
- tuple
- enum
- any
- object
- void
- null
- undefined
- never

### Coding Style

Karena didesain oleh Microsoft, TypeScript memiliki gaya penulisan kode ala-ala `C#`, tapi karena saya belum pernah nulis di `C#`, maka saya anggap kalau TypeScript mirip `Kotlin` :thinking_face:

Namun ada keanehan dari gaya penulisan typescript ini, untuk deklarasi tipe untuk `variable`, digunakan tanda colon (titik dua) `:` untuk menjelaskan tipe data mana yang diturunkan untuk variable tersebut. Sedangkan untuk penurunan class atau implementasi dari `interface` masih menggunakan literal keyword `extends` dan `implements`. Misal

```
interface User {
  username: string,
  password: string,
  full_name: string
}

var user: User = {
  username: 'jodoe',
  password: 'kelincipercobaan',
  full_name: 'Jonathan Doe
}
```

```
interface Fly {
  doFly(): void
}

class Plane {
  has_wings: boolean,
  engine_count: number
}

class Boeing extends Plane implements Fly {
  brand_name: string

  doFly(): void {
    console.log('I am flying');
  }
}
```

### Abussive Interfaces Usage

Selama saya menulis di TypeScript untuk proyek yang saya kerjakan, hal yang paling mencolok adalah banyaknya `interface` yang saya buat bahkan untuk satu buat `class` saya bisa membuat minimal 3 berkas interface, diantaranya interface untuk `implements`, `request` dan `response`. Kelebihan dari penggunaan `interface` ini adalah kita tidak harus menebak-nebak lagi tipe data yang digunakan untuk masing-masing variable, object atau key, karena memang sudah ditentukan pada interface.

### Module Loader is A Key, but I don't Need it Anyway

Ada beberapa jenis `module` loader yang bisa dipakai untuk memanggil berkas keluaran dari project TypeScript, diantaranya

- commonjs
- amd
- es6
- es2015
- system
- umd

Tapi karena beberapa keterbatasan dan ukuran project sudah terlalu besar untuk re-code, maka dalam project ini saya tidak menggunakan module loader apapun, atau diisi `none` di parameter `module` di `tsconfig`. Imbasnya adalah, saya tidak bisa `extend` untuk tipe `class` walau `interface` masih memungkinkan, untuk `implements` masih bisa dipakai. Sebagai gantinya, saya gunakan [namespace](https://www.typescriptlang.org/docs/handbook/namespaces.html).

### Penutup

Berikut catatan saya tentang typescript, walau terlihat tidak penting, setidaknya saya punya catatan biar tidak lupa.