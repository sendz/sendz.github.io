---
layout: post
title: Arsitektur Proyek ReactJS dengan MVVM Pattern
type: post
date: 2018-09-24 15:16:00 +07:00
published: true
categories: programming
tags: reactjs, react, javascript, web, programming, mvvm
author: Sendy
comments: true
---

![React JS]({{ site.baseurl }}/assets/react.png)

*saya malas bikin gambar baru*

Kelanjutan dari pos [Mengelola Proyek ReactJS agar Lebih Baik]({{ site.baseurl }}/community/2018/07/24/mengelola-proyek-reactjs-yang-baik.html), ternyata saya belum puas dengan struktur yang ada sekarang, saya masih ingin semua bisa dites dengan unit test dengan cara yang lebih mudah.

Agar lebih mudah dalam membayangkan keadaan proyek saya saat ini, secara garis besar beban proyeknya sebagai berikut

* Proyek saya akan mendukung multi-bahasa kedepannya, jadi sudah ada berkas untuk Bahasa Indonesia dan Bahasa Inggris sesuai konfigurasi yang sudah ditentukan, dan nantinya akan ada *switcher* untuk mengganti bahasa tampilan.
* Banyak kode *business requirement* dan *logic code* di berkas `tsx` (karena menggunakan TypeScript, singkatnya `jsx` jika tanpa TypeScript yang selanjutnya akan saya sebut dengan istilah *View*).
* Kami dalam tim sudah memikirkan untuk menambahkan report coverage untuk setiap *Component*, *Screen* dan *Page*, serta modul-modul lainnya.

Dan kendala yang dihadapi adalah:

* Kami terlalu malas untuk menulis *unit test*.
* Jest sebagai *library* yang kami gunakan performanya tidak sesuai harapan, dan kami mengalami sedikit kesulitan saat menggunakannya.
* Untuk menguji *logic code* yang ada di *View* membutuhkan banyak langkah sehingga akan memakan banyak tenaga dan waktu, kenapa?
  * Karena untuk menguji kode dengan cara seperti ini, kita developer harus menjadi sebuah browser terlebih dahulu, yang artinya kita perlu memanipulasi dengan mensimulasikan aksi-aksi yang dilakukan oleh user di dalam browser hanya untuk menguji hasil dari sebuah *business logic*.
* Tidak ada waktu banyak untuk menulis *unit test*.

Kemudian saya memiliki ide untuk mengubah struktur kode yang sudah ada, dengan menambahkan pattern MVVM ke dalamnya. Secara garis besar, saya akan memiliki 1 atau 2 berkas baru untuk setiap komponen yang ada, yaitu *Model* dan *ViewModel*, dan apa yang akan dilakukan oleh keduanya bisa digambarkan seperti ini:

```Model <-> ViewModel <-> View```

![MVVM Schema]({{ site.baseurl }}/assets/mvvm-schema.png)

*Gambar dari [dotnetcurry.com](https://www.dotnetcurry.com/xamarin/1382/mvvm-in-xamarin-forms)*

* **Model** merepresentasikan Data dan State untuk masing-masing komponen, tetapi tidak menutup kemungkinan untuk digunakan ulang di komponen lain. Bisa berupa sebuah file atau hanya attached interface di *ViewModel*
* **ViewModel** bertugas untuk mengolah data yang dibutuhkan oleh *View*, selain itu *ViewModel* juga menyajikan fungsi-fungsi untuk memanipulasi data yang ada pada *Model*.
* **View** menampilkan data-data *observable* dan *actions* yang disediakan oleh *ViewModel*.

Ada perbedaan antara MVVM yang saya implementasikan dengan kebiasaan umum, yaitu saya menempatkan *actions* di *ViewModel* ketimbang di *Model*, karena saya ingin menjaga agar *Model* berfungsi sebagai *data holder* saja.

Tujuan dengan digunakannya pola arsitektur seperti ini adalah untuk memindahkan semua *business logic* dari *View* sehingga kami tidak perlu menulis *unit test* lagi untuk berkas *View*.

Hal-hal yang perlu diperhatikan:

* Saya masih menggunakan `mobx` untuk state manager, jadi untuk library lain mungkin butuh penyesuaian.
* Berkas *View* tidak boleh memiliki kode logika yang berkaitan dengan *business logic* atau *data*, *logic* hanya diperkenankan untuk manipulasi tampilan.
* Berkas *Model* yang saya implementasikan hanya berisi interface yang merepresentasikan data-data yang dibutuhkan oleh *ViewModel*, tapi tidak hanya terbatas pada interface saja. *Model* bisa saja digunakan sebagai data holder, memiliki `variable`, `setter` dan `getter` misal, tetapi manipulasi data hanya dilakukan dari dalam *ViewModel*.
  * Alasan saya hanya menggunakan *Model* sebagai `interface` karena tidak semua isi dari `DataType` saya gunakan, dan ukuran datanya masih terlalu kecil untuk membuatnya jadi sebuah modul independen terpisah, jadi implementasi yang saya terapkan hanyalah setengah-setengah dari konsep MVVM yang sesungguhnya(?).
* Berkas *ViewModel* harus bersih dari hal-hal yang berkaitan dengan *View*, misal tidak menggunakan tipe dari `React` atau `React.Component`, tidak ada `JSX`, jadi murni `JS` atau `TS`, kenapa? Agar mudah ketika membuat *unit test*.
* *Model* dan *ViewModel* tidak mengikat pada suatu *View* tertentu, dan memungkinkan untuk digunakan ulang (*reuse*).

Keuntungan-keuntungan yang didapatkan dengan pola seperti ini:

* Tim bisa bekerja dengan metode TDD atau *Test Driven Development*, rujukan bisa dicek di [Wikipedia - Test-driven development](https://en.wikipedia.org/wiki/Test-driven_development) yang juga memiliki beberapa keuntungan, diantaranya:
  * Developer fokus pada mencari solusi untuk pemecahan masalah
  * Mengurangi kegiatan-kegiatan tidak perlu karena menambah kode yang memang tidak perlu dalam penyelesaian masalah
  * Menjaga unit kode tetap kecil dan mudah dalam maintenance
* Angka *Code Coverage* yang bikin kita nyengir walau saya bukan penganut paham "harus ada code coverage", tapi kan *just in case* :grin:
* *Maintenance* yang lebih mudah, karena fokus developer tidak akan terbagi dalam satu waktu, misal fokus terpecah antara maintenance *logic code* dan *UI code*.
* Dengan adanya *ViewModel*, component *View* jadi seolah-olah *stateless*, karena *'state'* sudah berpindah ke *ViewModel*.

### Contoh Implementasi di ReactJS dengan Mobx

> Silakan sesuaikan dengan library atau state management yang anda gunakan

Berkas `/src/screen/login/Login.tsx` sebagai *View*
```
import * as React from 'react';
import {
  Button,
  Form,
  Grid,
  Header,
  Menu,
  Radio,
  Segment
} from 'semantic-ui-react';
import { EN, ID } from './languages';
import { inject, observer } from 'mobx-react';
import { INJECTIONS } from '../../constants';
import { InputPhone } from '../../components/InputPhone/InputPhone';
import { LocaleClass } from '../../states';
import { SelectLanguage } from '../../components/SelectLanguage/SelectLanguage';
import { ViewModel } from './ViewModel';
import './style.css';

interface Props {
  [INJECTIONS.LOCALE]?: LocaleClass;
  default?: any;
}

@inject(
  INJECTIONS.LOCALE
)
@observer
export class Login extends React.Component<Props> {

  viewModel: ViewModel;

  constructor(props: Props) {
    super(props);
    this.viewModel = new ViewModel({
      isUsePhoneNumber: false,
      locale: this.props[INJECTIONS.LOCALE]!.state
    });
  }

  render() {
    return (
      <div className="login-form">
        <Grid style={{ height: '10%' }}>
          <Grid.Column textAlign="right">
            <Menu
              secondary={true}
            >
              <Menu.Menu position="right" style={{ margin: 10 }}>
                <SelectLanguage />
              </Menu.Menu>
            </Menu>
          </Grid.Column>
        </Grid>
        <Grid textAlign="center" style={{ height: '90%' }} verticalAlign="middle">
          <Grid.Column style={{ maxWidth: 450 }}>
            <Header as="h2" color="teal" textAlign="center">
              {this.viewModel.title}
            </Header>
            <Form>
              <Segment>
                {this.viewModel.isUsePhoneNumber ?
                  <InputPhone
                    onChange={(data: any) => console.log(data.value)}
                  />
                  :
                  <Form.Input
                    fluid={true}
                    icon="user"
                    iconPosition="left"
                    placeholder={this.viewModel.emailPlaceholder}
                  />
                }
                {this.viewModel.isVerificationCodeSent ?
                  <Form.Input
                    fluid={true}
                    icon="key"
                    iconPosition="left"
                    placeholder={this.viewModel.passwordPlaceholder}
                  />
                  :
                  <Grid
                    textAlign="left">
                    <Grid.Column style={{ margin: `0 0 1em` }}>
                      <Radio
                        label={this.viewModel.phoneToggleLabel}
                        onChange={(_, { checked }) => this.viewModel.usePhoneNumber(checked!)}
                        toggle={true}
                      />
                    </Grid.Column>
                  </Grid>
                }
                <Button
                  color="teal"
                  fluid={true}
                  onClick={() => this.viewModel.verificationCodeSent(true)}
                  size="large"
                >
                  {this.viewModel.primaryButtonLabel}
                </Button>
              </Segment>
            </Form>
          </Grid.Column>
        </Grid>
      </div>
    );
  }
}
```

Bisa diperhatikan pada baris kode berikut, *View* akan menginisiasi `ViewModel` sebagai object `viewModel`, `ViewModel` haruslah berbentuk pure native code, atau kode pemrograman murni tanpa ada *markup language*.

```
this.viewModel = new ViewModel({
  isUsePhoneNumber: false,
  locale: this.props[INJECTIONS.LOCALE]!.state
});
```

Berkas `/src/screen/login/ViewModel.ts` sebagai *ViewModel* dan juga *Model*
```
import { observable, action, computed } from 'mobx';
import { LocaleInterface } from '../../states';
import { EN, ID, Language } from './languages';

interface Model {
  isUsePhoneNumber: boolean;
  isVerificationCodeSent?: boolean;
  locale: LocaleInterface;
}

interface Text {
  en: Language;
  id: Language;
}

export class ViewModel {

  @observable
  model: Model;

  @observable
  text: Text;

  constructor(model: Model) {
    this.initiateModel(model);
    this.initTextFiles();
  }

  @action
  initTextFiles = () => {
    this.text = {
      en: EN,
      id: ID
    };
  }

  @action
  initiateModel = (model: Model): void => {
    this.model = model;
  }

  @computed
  get isUsePhoneNumber(): boolean {
    return this.model.isUsePhoneNumber;
  }

  @action
  usePhoneNumber = (use: boolean): void => {
    this.model.isUsePhoneNumber = use;
  }

  @computed
  get isVerificationCodeSent(): boolean {
    return this.model.isVerificationCodeSent!;
  }

  @action
  verificationCodeSent = (sent: boolean): void => {
    this.model.isVerificationCodeSent = sent;
  }

  @computed
  get title(): string {
    return this.text[this.model.locale.language].TITLE;
  }

  @computed
  get emailPlaceholder(): string {
    return this.text[this.model.locale.language].EMAIL_PLACEHOLDER;
  }

  @computed
  get phoneToggleLabel(): string {
    return this.text[this.model.locale.language].TOGGLE_LABEL;
  }

  @computed
  get sendVerificationLabel(): string {
    return this.text[this.model.locale.language].SEND_VERIFICATION_CODE;
  }

  @computed
  get passwordPlaceholder(): string {
    return this.text[this.model.locale.language].INPUT_PASSWORD_PLACEHOLDER;
  }

  @computed
  get loginButtonLabel(): string {
    return this.text[this.model.locale.language].LOGIN_BUTTON_LABEL;
  }

  @computed
  get primaryButtonLabel(): string {
    return this.isVerificationCodeSent ? 
      this.loginButtonLabel :
      this.sendVerificationLabel;
  }
}
```

Berkas `/src/screen/login/ViewModel.test.ts` sebagai *Unit Test* untuk *ViewModel*

```
import { EN, ID } from './languages';
import { Locale } from '../../states';
import { ViewModel } from './ViewModel';

describe('ViewModel of Login', () => {
  let viewModel: ViewModel;
  beforeEach(() => {
    viewModel = new ViewModel({
      isUsePhoneNumber: false,
      isVerificationCodeSent: false,
      locale: Locale.state
    });
  });

  it('isUsePhoneNumber initiated', () => {
    expect(viewModel.isUsePhoneNumber).toBeFalsy();
  });

  it('isUsePhoneNumber set to true', () => {
    viewModel.usePhoneNumber(true);
    expect(viewModel.isUsePhoneNumber).toBeTruthy();
  });

  it('isVerificationCodeSent initiated', () => {
    expect(viewModel.isVerificationCodeSent).toBeFalsy();
  });

  it('isVerificationCodeSent set to true', () => {
    viewModel.verificationCodeSent(true);
    expect(viewModel.isVerificationCodeSent).toBeTruthy();
  });

  describe('Text in EN', () => {
    beforeAll(() => {
      Locale.setLocale('en-US');
    });

    it('title', () => {
      expect(viewModel.title).toEqual(EN.TITLE);
    });

    it('emailPlaceholder', () => {
      expect(viewModel.emailPlaceholder).toEqual(EN.EMAIL_PLACEHOLDER);
    });

    it('phoneToggleLabel', () => {
      expect(viewModel.phoneToggleLabel).toEqual(EN.TOGGLE_LABEL);
    });

    it('sendVerificationLabel', () => {
      expect(viewModel.sendVerificationLabel).toEqual(EN.SEND_VERIFICATION_CODE);
    });

    it('passwordPlaceholder', () => {
      expect(viewModel.passwordPlaceholder).toEqual(EN.INPUT_PASSWORD_PLACEHOLDER);
    });

    it('loginButtonLabel', () => {
      expect(viewModel.loginButtonLabel).toEqual(EN.LOGIN_BUTTON_LABEL);
    });

    describe('primaryButtonLabel', () => {
      it('should show sendVerificationLabel', () => {
        viewModel.verificationCodeSent(false);
        expect(viewModel.primaryButtonLabel).toEqual(viewModel.sendVerificationLabel);
      });

      it('should show loginButtonLabel', () => {
        viewModel.verificationCodeSent(true);
        expect(viewModel.primaryButtonLabel).toEqual(viewModel.loginButtonLabel);
      });

    });

  });

  describe('Text in ID', () => {
    beforeAll(() => {
      Locale.setLocale('id-ID');
    });

    it('title', () => {
      expect(viewModel.title).toEqual(ID.TITLE);
    });

    it('emailPlaceholder', () => {
      expect(viewModel.emailPlaceholder).toEqual(ID.EMAIL_PLACEHOLDER);
    });

    it('phoneToggleLabel', () => {
      expect(viewModel.phoneToggleLabel).toEqual(ID.TOGGLE_LABEL);
    });

    it('sendVerificationLabel', () => {
      expect(viewModel.sendVerificationLabel).toEqual(ID.SEND_VERIFICATION_CODE);
    });

    it('passwordPlaceholder', () => {
      expect(viewModel.passwordPlaceholder).toEqual(ID.INPUT_PASSWORD_PLACEHOLDER);
    });

    it('loginButtonLabel', () => {
      expect(viewModel.loginButtonLabel).toEqual(ID.LOGIN_BUTTON_LABEL);
    });

    describe('primaryButtonLabel', () => {
      it('should show sendVerificationLabel', () => {
        viewModel.verificationCodeSent(false);
        expect(viewModel.primaryButtonLabel).toEqual(viewModel.sendVerificationLabel);
      });

      it('should show loginButtonLabel', () => {
        viewModel.verificationCodeSent(true);
        expect(viewModel.primaryButtonLabel).toEqual(viewModel.loginButtonLabel);
      });
    });
  });
});
```

Dengan mengimplementasi pola ini, kita bisa mengabaikan *test coverage* dari *View* (jika diperlukan), karena seluruh *logic code* bahkan yang digunakan di *View* sudah ditest pada *ViewModel*, dan kita bisa menentukan apa yang akan tampil di *View* melalui *ViewModel*.

Dan jangan lupa untuk konfigurasi ulang *code coverage* untuk reporting, hal ini dilakukan untuk memilih berkas mana saja yang akan kita masukkan untuk komparasi antara kode dengan *unit testing*, agar hasil dari *code coverage* muncul dan hasilnya bisa kita ketahui.

Contoh konfigurasi yang saya gunakan di `package.json`
```
{
  ...,
  "jest": {
    "coverageReporters": [
      "lcov",
      "json",
      "text"
    ],
    "collectCoverageFrom": [
      "**/*.{ts}",
      "!**/node_modules/**",
      "!**/vendor/**",
      "!**/Model.ts",
      "!**/CONSTANT/*.*",
      "!**/constants/*.*",
      "!**/languages/*.*",
      "!**/index.tsx",
      "!**/index.ts"
    ]
  }
}
```

Dan hasil akhirnya, struktur proyek saya akan berbentuk seperti ini:
```
/src
  /components
    /Header
      Header.tsx
      index.ts
    /LocationPicker
      index.ts
      language.en.ts
      language.id.ts
      LocationPicker.tsx
      ViewModel.test.ts
      ViewModel.ts
    /BankInfo
      BankInfo.tsx
      index.ts
      language.en.ts
      language.id.ts
      ViewModel.test.ts
      ViewModel.ts
    /Status
      /assets
        /images
          logo.png
      index.ts
      language.en.ts
      language.id.ts
      StatusApproved.tsx
      StatusConfirmed.tsx
      StatusRejected.tsx
      ViewModel.test.ts
      ViewModel.ts
    /Navigation
      index.ts
      language.en.ts
      language.id.ts
      Navigation.tsx
      ViewModel.test.ts
      ViewModel.ts
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
      Confirmation.tsx
      ViewModel.test.ts
      ViewModel.ts
    /Onboarding
      /assets
        /images
          logo.svg
      /pages
        /Agreement
          index.ts
          language.en.ts
          language.id.ts
          Agreement.tsx
          ViewModel.test.ts
          ViewModel.ts
        /OutletInfo
          index.ts
          language.en.ts
          language.id.ts
          OUTLET_CITY.ts
          OutletInfo.tsx
          ViewModel.test.ts
          ViewModel.ts
        /UserInfo
          index.ts
          language.en.ts
          language.id.ts
          UserInfo.ts
          ViewModel.test.ts
          ViewModel.ts
      index.ts
      language.en.ts
      language.id.ts
      Onboarding.tsx
      ViewModel.test.ts
      ViewModel.ts
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