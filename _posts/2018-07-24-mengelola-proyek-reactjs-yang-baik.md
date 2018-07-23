---
layout: post
title: Mengelola Proyek ReactJS agar Lebih Baik
type: post
date: 2018-07-24 00:43:00 +07:00
published: true
categories: community
tags: reactjs, react, javascript, web, programming
author: Sendy
comments: true
---

![React JS]({{ site.baseurl }}/assets/react.png)

Setelah setahun lebih menggunakan AngularJS untuk membuat aplikasi Web Frontend, saya bergabung dengan tim baru untuk mengelola Frontend dengan ReactJS yang sempat saya pelajari sedikit sebelumnya walau belum khatam. Awalnya di tim baru tersebut saya menyarankan untuk dipindahkan ke VueJS karena beban kerjanya masih belum besar dengan beberapa pertimbangan kemudahan pengembangan dengan VueJS, tetapi pada akhirnya kami tetap jalan dengan ReactJS karena salah satu alasannya tim design sudah membuatkan Component Library yang siap digunakan untuk ReactJS. Saya merasa tertantang jadinya.

> Catatan 1: Kami menggunakan mobx untuk State Management, kalau belum pernah berkenalan dengan mobx, dokumentasi bisa dibaca di [https://mobx.js.org/](https://mobx.js.org/)

> Catatan 2: Contoh yang digunakan di sini menggunakan ReactJS dengan TypeScript sebagai framework dan transpiler, tidak menutup kemungkinan bisa disesuaikan untuk Angular, AngularJS, VueJS, dan lain-lain.

> Catatan 3: Hasil akhir dari studi kasus ini belumlah sempurna, saya masih merasa ada hal yang kurang yang bisa diefisiensikan lagi, masih ada legacy code yang terasa masih semrawut, tapi untuk saat ini dengan paham saya yang menganut `get the shit done` dan `think simple`, pada _state_ saat ini saya rasa sudah cukup.

### Permasalahan dalam membangun aplikasi

Masalah dalam pengembangan aplikasi yang biasa terjadi dan dialami teman-teman developer memiliki pola yang mirip-mirip

1. Klien (Tergantung dari struktur organisasi di mana kita bekerja, bisa saja Klien di sini adalah Product Owner, Sales, atau bahkan CEO) butuh sebuah produk berupa aplikasi, kita buatkan. [Check]
2. Bekerja dalam tim, semua bekerja dengan baik. [Check]
3. Penambahan fitur baru dan perbaikan. [Check]
4. Penambahan fitur baru menyebabkan masalah di tempat lain, butuh perbaikan lainnya. [Check]
5. Perbaikan yang dilakukan memunculkan masalah baru yang tidak kita duga. [Check]
6. Masalah yang ada berhasil ditangani dengan baik, tidak ada masalah yang cukup mengganggu dan berdampak pada konsumen produk. [Check]
7. Klien menyadari kalau ada fitur yang dulu dia inginkan ternyata tidak terlalu berguna, atau bahkan tidak pernah digunakan oleh konsumen, lalu kita diminta membuangnya. [Check]
8. Setelah penambahan ini-itu, penambalan sana-sini, perbaikan begitu-begini, akhirnya kita sadar bahwa beban kerja pada struktur proyek sudah sangat besar, kita menyadari bahwa kita sering hilang arah saat mengembangkan produk tersebut, sampai pada suatu kondisi di mana kita cuma pasrah dan bilang "Yaudah sih masih jalan ini".
9. Waktu demi waktu, kode kita beneran acak-acakan gak karuan, orang luar menyebutnya _spaghetti code_, kita bisa menyebut _kode indomie_, keriting!

Ya, saya dan tim beberapa waktu yang lalu mengalami masalah ini, meski skalanya belum besar, tapi bersyukurlah karena belum besar, usaha yang dibutuhkan juga tidak terlalu besar. Kesempatan itu muncul saat tim desain mengajukan desain baru dari segi UI dan UX, saya melihat itu sebagai peluang untuk memperbaiki sedikit-demi sedikit masalah yang kami lakukan karena proyek kami tidak didesain dengan baik di awal.

### Awal Perjalanan

Saya beberapa kali menjelajah mesin pencarian, beberapa hal yang sering disebutkan dalam petunjuk awal untuk mengelola proyek ReactJS adalah _Thinking in React_ yang belum saya lakukan secara maksimal karena masih belum paham konsepnya. Selain itu, kami menginisiasi proyek ini dengan pengetahuan yang minim tentang ReactJS, istilahnya biar nyemplung sekalian basah lalu berenang.

Dengan segala keterbatasan pengetahuan dan waktu karena dikejar deadline, jadilah struktur proyek kami seperti ini:

```
/src
  /assets
    /fonts
    /images
    /styles
      custom.style.scss
  /components
    /header
      Header.tsx
    /modal
      /dashboard
        /confirmation
          Approved.tsx
          Complete.tsx
          Rejected.tsx
        /forms
          Agreement.tsx
          OutletInfo.tsx
          UserInfo.tsx
        Registration.tsx
    /sidebar
      Header.tsx
      Menu.tsx
    App.tsx
    Dashboard.tsx
    Home.tsx
    Login.tsx
    Sidebar.tsx
  /helpers
    dataHelper.ts
    nameHelper.ts
    validationHelper.ts
  /languages
    en.ts
    id.ts
  /services
    analyticsService.ts
    apiService.ts
  /states
    appState.ts
    languageState.ts
  /stores
    appStore.ts
    userStore.ts
  /test
    /__stubs__
    /components
      **/*.test.tsx
    /**/*.test.ts
  index.ts
```

Sekilas tidak ada yang salah, dalam tulisan [Nathanael Beisiegel](http://engineering.kapost.com/2016/01/organizing-large-react-applications/), pola dalam pengembangan terutama yang menggunakan basis ReactJS secara garis besar ada 2 macam, yaitu
1. File-Type First
2. Feature First.

#### File-Type First

File-Type First _pattern_ secara sederhananya adalah mengatur berkas-berkas aplikasi menurut fungsinya, dan setiap jenis fungsi dari berkas-berkas disimpan di bawah satu folder induk, misal seluruh berkas yang berisi React Component akan berada di dalam `/components`, setiap berkas tooling akan berada di folder `/tools`, berkas _store_ untuk mobx ada di `/stores` atau untuk _reducers_ redux ada di folder `/reducers`. Contoh strukturnya adalah struktur proyek yang saya jabarkan di atas.

Kelebihan File-Type First ini diantaranya

* Mudah diinisiasi.
* Kita tidak perlu bingung dimana menyimpan masing-masing berkas kode, urutkan saja sesuai dengan fungsinya.
* Saat _import_ beberapa file yang sama type-nya, kita tahu ke mana harus mengarahkan lokasi foldernya.
* Struktur proyek lebih mudah dipahami jika ada anggota tim baru bergabung.

Di balik kelebihannya, ada beberapa kekurangan dengan pattern ini saat skala proyek kita sudah cukup membesar

* _Naming Convention_ yang ternyata lebih sulit dibanding memberi nama sebuah fitur saat ingin membuat modul baru, kami ingin nama file / class baru tersebut memiliki arti yang mudah dipahami tanpa harus mengisinya dengan _comment_ hanya untuk menjelaskan apa isinya.
* Dengan dipisahnya file teks untuk masing-masing bahasa, kami jadi lebih sulit untuk mengelola isi dari file tersebut, bahkan struktur objek pun hampir membuat saya bingung mau diapakan, apakah setiap string harus dikumpulkan dalam satu _object_ sesuai fungsi dan penempatannya? Atau cukup nama _field_ nya dikasih _suffix_? Hal seperti ini menjadi problematika yang walaupun sudah ditentukan seringnya mengganjal.
* Menjadikan kami melakukan hal yang tidak perlu karena memisahkan fungsi-fungsi ke modul baru yang sebenarnya tidak perlu karena hanya dipanggil sekali dan di satu tempat.
* Susah dioptimasi dan jadi terlalu banyak magic trick di dalam code.

#### Feature First

Terinspirasi dari konsep _Pods_-nya Ember, di mana semua file dikelompokkan menurut fiturnya dan menjadi _top-level folder_ dan setiap pod bisa berisi file apapun yang dibutuhkan oleh pod tersebut.

Contoh seperti ini:

```
app/
  authentication/
    api/
    components/
    helpers/
    ...
  comments/
    actions/
    api/
    components/
    reducers/
    stores/
    ...
  ...
```

Saya mengajukan untuk mengubah struktur menjadi _Feature First_ yang saya sesuaikan lagi strukturnya dengan beberapa pertimbangan

* Saya merestruktur proyek sendirian, karena anggota tim yang lain _maintain_ kode lama.
* Saya ingin mempertahankan ciri khas struktur proyek pada ReactJS agar anggota tim tidak harus banyak beradaptasi ulang dengan struktur baru, jadi beberapa hal dari struktur lama saya pertahankan.
* Kembali ke tujuan awal kami agar beban dalam perawatan jadi lebih mudah, maka saya harus berpikir sederhana.

Apa yang saya lakukan adalah
1. Memisahkan komponen-komponen yang berulang di beberapa tempat menjadi satu paket komponen baru yang disimpan di folder `/components` dan diperlakukan sebagai sebuah fitur.
2. Memindahkan halaman-halaman dari folder `/components` ke `/screens` yang juga diperlakukan sebagai fitur, walaupun keduanya merupakan `extends` dari `React.Component`, tapi saya ingin memberikan pandangan bahwa apa yang ada di dalam `/components` adalah komponen yang _reusable_ atau berpotensi untuk _reusable_ dan apa yang ada di dalam `/screens` adalah layar utama dari aplikasi yang di dalamnya ada folder `pages` sebagai anak dari masing-masing _screen_.
3. _Nested_ komponen dan halaman seperlunya.
4. Saya masukkan apapun yang dibutuhkan masing-masing _component_, _screen_, atau _page_ ke masing-masing folder seperti file teks untuk masing-masing bahasa, styling, asset gambar, hingga file unit test. Tujuannya apa lagi selain agar lebih mudah dalam maintenance?
5. Tidak ada lagi global file untuk teks tiap bahasa, semua komponen harus mandiri untuk hal ini.
6. Untuk _stores_, _services_, _states_, _helpers_, _interfaces_ strukturnya masih saya pertahankan dengan tambahan file unit test di dalamnya, tidak saya buatkan sub-folder dan saya biarkan mereka berkumpul dalam satu folder saja, mungkin kedepannya saya akan buatkan.

Hasil akhir dari restrukturisasi adalah sebagai berikut

```
/src
  /components
    /Header
      Header.test.tsx
      Header.tsx
      index.ts
    /LocationPicker
      index.ts
      language.en.ts
      language.id.ts
      LocationPicker.test.tsx
      LocationPicker.tsx
    /BankInfo
      BankInfo.test.tsx
      BankInfo.tsx
      index.ts
      language.en.ts
      language.id.ts
    /Status
      /assets
        /images
          logo.png
      index.ts
      language.en.ts
      language.id.ts
      StatusApproved.test.tsx
      StatusApproved.tsx
      StatusConfirmed.test.tsx
      StatusConfirmed.tsx
      StatusRejected.test.tsx
      StatusRejected.tsx
    /Navigation
      index.ts
      language.en.ts
      language.id.ts
      Navigation.test.tsx
      Navigation.tsx
  /constants
    GLOBAL_CONSTANT.ts
  /helpers
    dataHelper.test.ts
    dataHelper.ts
    nameHelper.test.ts
    nameHelper.ts
    validationHelper.test.ts
    validationHelper.ts
  /screens
    /Confirmation
      Confirmation.test.tsx
      Confirmation.tsx
    /Onboarding
      /assets
        /images
          logo.svg
      /pages
        /Agreement
          index.ts
          language.en.ts
          language.id.ts
          Agreement.test.tsx
          Agreement.tsx
        /OutletInfo
          index.ts
          language.en.ts
          language.id.ts
          OUTLET_CITY.ts
          OutletInfo.test.tsx
          OutletInfo.tsx
        /UserInfo
          index.ts
          language.en.ts
          language.id.ts
          UserInfo.test.tsx
          UserInfo.ts
      index.ts
      language.en.ts
      language.id.ts
      Onboarding.test.tsx
      Onboarding.tsx
    App.tsx
  /services
    analyticsService.test.ts
    analyticsService.ts
    apiService.test.ts
    apiService.ts
  /states
    appState.test.ts
    appState.ts
    languageState.test.ts
    languageState.ts
  /stores
    appStore.test.ts
    appStore.ts
    userStore.test.ts
    userStore.ts
  /test
    /__stubs__
      *.test.ts
  root.tsx
  setupTest.ts
```

Dengan struktur baru seperti di atas, setiap _screen_ maupun _component_ sudah memiliki apa yang dibutuhkan oleh masing-masing komponen, seperti style, gambar, translasi, unit test dan integration test sebagai sebuah _**fitur**_ tanpa harus memiliki ketergantungan antar fitur. Dengan begini, setiap fitur akan menjadi fitur mandiri yang modular.

Berikut pakem yang harus diikuti dalam struktur baru adalah:

* _component_ adalah sebuah modul `extends` dari `React.Component` yang memiliki scope yang kecil dan sederhana.
* _screen_ adalah layar utama dari sebuah fitur, misal _screen_ untuk Login atau Register.
* _page_ adalah halaman yang memuat _page_ lain atau _component_.
* _helper_ adalah script tooling yang tidak melakukan permintaan/koneksi ke server.
* _store_ adalah file yang mengelola _mobx store_, umumnya berisi model sebagai state dan API call.
* _state_ adalah file yang mengelola model berupa local state dalam aplikasi dan tidak memiliki fungsi API call.
* _service_ adalah file yang mengelola API call tanpa mewajibkan adanya state.
* _test_ adalah file unit test dan integration test.
* File `index.ts` adalah file yang berfungsi untuk `exporting` semua modul yang akan digunakan dari folder fitur tersebut. Contoh ada di bawah.
* Sebuah _component_ bisa berinduk pada _component_, _helper_ atau _service_ dan tidak bisa berinduk pada _screen_ atau _pages_, jadi di sini _component_ adalah sebuah modul tersendiri.
* Berlaku ketentuan _single screen_, jadi _screen_ tidak boleh berinduk pada apapun atau independen.
* Sebuah _page_ bisa berinduk pada _screen_, _page_ atau _component_, akan tetapi _component_ tidak bisa berinduk pada _page_ dengan tujuan agar _component_ memiliki scope sesederhana mungkin.
* Sebuah fitur yang berinduk hanya bisa digunakan oleh induknya saja dengan tingkatan 1 level dan berlaku sebaliknya.
* Tidak berlaku fitur yang memiliki induk yang berbeda dapat saling mengakses, seperti menikahi sepupu. Jika dibutuhkan, pindahkan fitur tersebut ke induknya atau ke _component_, _helper_ atau _service_ yang bersifat global sesuai peruntukannya.
* Hindari menyimpan fitur di global _helper_, _service_, _state_ atau _store_ jika hanya digunakan sekali saja, cukup simpan di fitur yang mengaksesnya, kecuali yakin bahwa fitur tersebut digunakan di tempat lain.
* Angka maksimum _nested_ folder adalah 3 dengan tujuan untuk mengurangi kompleksitas dan memaksimalkan _code reusability_.

Contoh penggunaan file `index.ts`:

```
export { StatusApproved } from './StatusApproved';
export { StatusConfirmed } from './StatusConfirmed';
export { StatusRejected } from './StatusRejected';
```

sehingga jika ada satu fitur yang ingin menggunakan ketiganya sekaligus, cukup _import_ dalam satu folder, misal:

```
import { StatusApproved, StatusConfirmed, StatusRejected } from '../../components/Status'
```

#### _Next Improvement_

Dengan struktur proyek baru seperti ini, banyak hal yang bisa kita lakukan untuk penyempurnaan, misal dengan menyematkan pattern MVP (Model-View-Presenter) atau MVVM (Model-View-ViewModel), atau bahkan MVW (Model-View-Whatever) jika memang dibutuhkan yang mungkin akan kami lakukan kedepannya.

