---
layout: post
title: Bermain Bersama Appium untuk Automation Testing Aplikasi Mobile
date: 2017-01-22 18:45:38 +07:00
type: post
published: false
categories: testing
author: Sendy
comments: true
---

Tentang Appium
---
Appium adalah sebuah framework open source untuk automation testing aplikasi native, hybrid dan mobile web application yang menggunakan protokol WebDriver. Appium bisa digunakan untuk iOS, Andrid dan aplikasi Windows.

Secara tebakan agak ngawur, appium akan mencari element-element yang tampil di layar dengan menggunakan metode pendekatan web DOM, setiap tampilan di layar diterjemahkan sebagai web element dan disimpan di protokol WebDriver, dan appium akan mengakses protokol WebDriver tersebut untuk diinspeksi dan diuji.

Selain Appium itu sendiri, dibutuhkan beberapa software lain

* `brew`
* `node` atau `nodejs` server
* `appium` framework
* `appium-doctor` untuk memverifikasi persiapan framework `appium`
* `cucumber` sebagai penerjemah bahasa `scenario`
* `jdk` atau `java development kit`
* Intellij IDEA Community Edition
* `android-sdk`
* `genymotion` atau `device`
* `uiautomatorviewer` sebagai UI Inspector yang sudah sepaket dengan Android SDK

Persiapan
---
Pada kasus ini, saya menggunakan OSX El Capitan, jadi akan dibahas langkahnya dengan cara OSX, yang pake linux bisa menyesuaikan.

### Install brew

`/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`

### Install node

`> brew install node`

### Install appium framework

`> npm install -g appium`

### Install appium client

`> npm install -g wd`

### Install appium-doctor

`> npm install -g appium-doctor`

### Install appium GUI

Unduh di [http://appium.io/](http://appium.io/) lalu salin `Appium` ke folder `Applications`

### Install Java

Unduh di [Java SE Development Kit 8 Downloads](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

### Verifikasi Semua Persiapan

Persiapan sudah selesai, mungkin. Coba buka terminal lalu jalankan `appium-doctor`, mungkin saat pertama akan ada hasil yang kurang memuaskan, banyak silang merah misal.

![Appium Doctor]({{ site.baseurl }}/assets/appium-doctor.png)

Mulai dari sini `WARN AppiumDoctor  ✖ Carthage was NOT found!`, Carhage bisa diunduh di [Carhatge Relase](https://github.com/Carthage/Carthage/releases) dan unduh berkas `.pkg` lalu install.

Lanjut ke `WARN AppiumDoctor  ✖ ANDROID_HOME is NOT set!`, coba export di mana `android-sdk` berada. Misal `export ANDROID_HOME=/Users/user/Library/Android/sdk`, lebih baik lagi jika disimpan di file `.bashrc`, `.bash_profile` atau `.zshrc` biar lebih mudah.

Lanjut lagi ke `WARN AppiumDoctor  ✖ JAVA_HOME is NOT set!` yaitu set `JAVA_HOME`, caranya sama dengan di atas, di terminal eksekusi perintah `export JAVA_HOME=/usr/bin/java`, lebih baik juga jika disimpan di file `.bashrc`, `.bash_profile` atau `.zshrc` biar lebih mudah.

Ternyata masih ada yang ini `WARN AppiumDoctor  ✖ Bin directory for $JAVA_HOME is not set`, yang ini lebih mudah harusnya, tinggal `export PATH=$PATH:$JAVA_HOME/bin`.

```
➜  ~ appium-doctor
info AppiumDoctor Appium Doctor v.1.3.0
info AppiumDoctor ### Diagnostic starting ###
info AppiumDoctor  ✔ The Node.js binary was found at: /usr/local/bin/node
info AppiumDoctor  ✔ Node version is 7.4.0
info AppiumDoctor  ✔ Xcode is installed at: /Library/Developer/CommandLineTools
info AppiumDoctor  ✔ Xcode Command Line Tools are installed.
info AppiumDoctor  ✔ DevToolsSecurity is enabled.
info AppiumDoctor  ✔ The Authorization DB is set up properly.
info AppiumDoctor  ✔ Carthage was found at: /usr/local/bin/carthage
info AppiumDoctor  ✔ HOME is set to: /Users/sendy
info AppiumDoctor  ✔ ANDROID_HOME is set to: /Users/sendy/Library/Android/sdk
info AppiumDoctor  ✔ JAVA_HOME is set to: /usr/bin/java
info AppiumDoctor  ✔ adb exists at: /Users/sendy/Library/Android/sdk/platform-tools/adb
info AppiumDoctor  ✔ android exists at: /Users/sendy/Library/Android/sdk/tools/android
info AppiumDoctor  ✔ emulator exists at: /Users/sendy/Library/Android/sdk/tools/emulator
info AppiumDoctor  ✔ Bin directory of $JAVA_HOME is set
info AppiumDoctor ### Diagnostic completed, no fix needed. ###
info AppiumDoctor
info AppiumDoctor Everything looks good, bye!
info AppiumDoctor
```
![Appium Doctor Success]({{ site.baseurl }}/assets/appium-doctor-green.png)

### Unduh IntelliJ IDEA Community Edition

Unduh di [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) lalu pilih yang `Community`, dan install.

Testing Scenario
---

Di bagian ini kita akan berkenalan tentang `testing scenario` agar skenario pengujian bisa dibaca oleh manusia, alias *human readable*.

Dalam pengujian, ada 3 syarat utama atau disebut *Three Principle of Testing* yaitu

1. **Initialization** atau **setup**.
2. **Trigger** atau eksekusi fungsi yang akan kita uji.
3. **Assertion** atau menguji apakah hasil sesuai ekspektasi pengujian.

Sebelumnya, kita akan mengenal gherkin language yang ditandai dengan prefix `Scenario:`, `Given`, `When`, `Then` dan `And` sebagai prefix sebuah baris skenario.

1. `Feature:` adalah deskripsi fitur yang akan ditulis
2. `Scenario:` adalah deskripsi dari skenario pengujian yang akan dilakukan.
3. `Given` adalah prefix untuk membuat baris skenario *initialization*.
4. `When` adalah prefix untuk membuat baris skenario *trigger*.
5. `Then` adalah prefix untuk membuat baris skenario *assertion*.
6. `And` adalah penghubung jika `Given`, `When`, `Then` memiliki lebih dari satu baris.

Studi Kasus
---

Studi kasus adalah Twitter Android, bisa diunduh di [APKMirror](http://www.apkmirror.com/apk/twitter-inc/twitter/twitter-6-31-0-beta-550-release/) lalu diinstall baik di Emulator atau device android.

Untuk saat ini, maksimum device yang bisa digunakan adalah API Level 22 atau Lollipop 5.1, yang menggunakan Marshmallow bisa cari device di bawahnya atau downgrade dulu.

Klik icon Android atau icon Robot Ijo yang khas, lalu
1. Isi App Path dengan lokasi APK twitter yang baru saja diunduh
2. Di kolom Capabilities isi
    * `Platform Name` dengan `Android`.
    * `Automation Name` dengan `Appium`.
    * `Platform Version` dengan versi android yang sesuai dengan device atau emulator
    * `Device Name` saya isi dengan nama emulator di genymotion, yaitu `nexus`

![Appium]({{ site.baseurl }}/assets/appium-gui.png)

Start server appium dengan klik icon appium di toolbar atas.

di terminal, ketik perintah `uiautomatorviewer` atau `./uiautomatorviewer`.

![UI Automator Viewer]({{ site.baseurl }}/assets/uiautomatorviewer.png)

Di sini kita bisa eksplorasi resource ID dari aplikasi android untuk memulai pengujian.

Menulis Sekenario
---

Mulanya, buatlah maven project baru di IntelliJ IDEA.

1. Buka `IntelliJ IDEA`
2. Pilih `Create New Project`
3. Pilih `Maven`
4. Klik `Create from archetype`
5. Pilih yang `maven-archetype-quickstart`
6. Klik `Next`
7. Isi GroupId dengan nama domain, misal `io.github.sendz`
8. Isi ArtifactId sesuai keinginan, misal `autotest`
9. Klik `Next`
10. Sampai sini klik `Next` lagi
11. Klik `Finish`
12. Tunggu sampai proses membuat project selesai
13. Tambahkan baris berikut di bawah pada file `pom.xml` dalam tag `<dependencies></dependencies>`

```
<dependency>
   <groupId>junit</groupId>
   <artifactId>junit</artifactId>
   <version>3.8.1</version>
   <scope>test</scope>
</dependency>
<dependency>
   <groupId>org.seleniumhq.selenium</groupId>
   <artifactId>selenium-java</artifactId>
   <version>3.0.1</version>
</dependency>
<dependency>
   <groupId>io.appium</groupId>
   <artifactId>java-client</artifactId>
   <version>4.1.2</version>
   <scope>test</scope>
</dependency>
<dependency>
  <groupId>info.cukes</groupId>
  <artifactId>cucumber-java</artifactId>
  <version>1.2.5</version>
</dependency>
```
14. Reimport `maven` project

Mulai menulis file feature untuk skenario kita.
1. Buka folder `src/test` lalu buat folder baru bernama `resources`
2. Buat file baru bernama `login.feature`
3. Tulis skenario fitur seperti

```
Feature: Twitter Login

  Scenario: Open Twitter login page
    Given I open twitter app
    When I click "Log In" button
    Then I on "Log In" page
```
4. Jika di OSX / Mac dengan Keymap `Mac OS X` tekan tahan `alt + enter`
5. Pilih `Create step definition` lalu beri nama file sesuai dengan nama file di feature
6.
