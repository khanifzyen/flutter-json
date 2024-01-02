# Flutter JSON

JSON (JavaScript Object Notation) adalah format pertukaran data yang ringan. Sangat mudah bagi manusia untuk membaca dan menulisnya. Mudah bagi mesin untuk memprosesnya dan membuatnya. Format ini didasarkan pada Standar Bahasa Pemrograman JavaScript ECMA-262 Edisi ke-3 - Desember 1999. JSON adalah format teks yang sepenuhnya terbebas dari bahasa pemrograman apapun, akan tetapi dalam prakteknya json ini menggunakan konvensi yang akrab dengan bahasa pemrograman dari keluarga bahasa C, termasuk C, C++, C#, Java, JavaScript, Perl, Python, dan masih banyak lainnya. Kemudahan dan keunggulannya ini menjadikan JSON sebagai bahasa pertukaran data yang ideal dan terkenal untuk saat ini.

![Gambar 1. Struktur JSON dibandingkan List/Array pada umumnya](img/01%20struktur%20json.PNG)

Gambar 1. Struktur JSON dibandingkan List/Array pada umumnya

Dalam topik ini nanti kita hanya akan membahas cara menggunakan JSON dengan Flutter. Ini nanti akan mencakup solusi yang diberikan oleh JSON dalam skenario yang berbeda, dan mengapa itu diperlukan.

Pengkodean (Encoding) dan serialisasi (serialization) adalah hal yang sama, mengubah struktur data menjadi string. Decoding dan deserialization adalah proses yang berlawanan, mengubah string menjadi struktur data. Namun, serialisasi juga biasanya mengacu pada keseluruhan proses penerjemahan struktur data ke dan dari format yang lebih mudah dibaca.

Untuk menghindari kebingungan, dalam topik ini kita akan menggunakan "serialization" saat mengacu pada keseluruhan proses, dan "encoding" - "decoding" saat secara khusus merujuk pada proses tersebut.

```dart
class User {
    final String name;
    final String email;

    User(this.name, this.email);

    User.fromJson(Map<String, dynamic> json)
        : name = json['name'],
          email = json['email'];

    Map<String, dynamic> toJson() => {
        'name': name,
        'email': email,
    };
}
...
String json = jsonEncode(user);
...
Map<String, dynamic> userMap = jsonDecode(jsonString);
var user = User.fromJson(userMap);
```

**Memilih Json Serialization yang tepat?**

1. Manual Serialization
2. Automated serialization menggunakan code generation

Proyek yang berbeda tentunya mempunyai kompleksitas dan kasus logic yang berbeda. Untuk proyek proof-of-concept yang sederhana atau prototipe, menggunakan code generator mungkin terlalu berlebihan. Dan juga untuk aplikasi dengan beberapa model JSON yang lebih kompleks, Manual serialization bisa menjadi membosankan, sering berulang, dan dapat menyebabkan lebih banyak kesalahan kesalahan kecil yang berimpact ke aplikasi.

**Manual Serialization atau proses decode encode secara manual ini cocoknya untuk project-project kecil**

## Manual Serialization

Decoding JSON secara manual mengacu pada penggunaan Json decoder bawaan dari `dart:convert`. Caranya memasukan string JSON ke dalam fungsi `jsonDecode()`, hasilnya nanti berupa `Map<String, dynamic>`. Dengan `Map<String,dynamic>` ini kita sudah dapat mencari nilai yang kita inginkan dalam bentuk object.

Decode manual tidak bekerja dengan baik saat proyek kita menjadi lebih besar. Menulis logika decoding secara manual bisa menjadikan kode kita sulit untuk dikelola dan rawan kesalahan. Jika Anda salah ketik saat mengakses kolom JSON yang tidak ada, kode kita akan menampilkan kesalahan selama runtime.

Jika kita tidak memiliki banyak model JSON dalam proyek kita dan hanya ingin menguji konsep dengan cepat, manual serialization mungkin merupakan cara yang tepat untuk dimulai. Untuk contoh manual encoding, lihat Serialisasi JSON secara manual menggunakan `dart:convert`.

### Flutter Basic JSON Serialization

```json
{
  "name": "John Smith",
  "email": "john@example.com"
}
```

Serialisasi JSON di Flutter sangatlah sederhana. Flutter memiliki library `dart:convert` bawaan yang menyediakan fungsi encoder dan decoder JSON secara langsung.

Contoh JSON berikut mengimplementasikan secara sederhana model `user`. Dengan `dart:convert`, kita dapat membuat serialize model JSON ini dengan dua cara:

1. Serializing JSON secara inline
2. Serializing JSON didalam class model

### 1. Serializing JSON inline

Dengan melihat dokumentasi `dart:convert`, kita akan melihat bahwa kita dapat mendekode JSON dengan memanggil fungsi `jsonDecode()`, dengan string JSON sebagai argumen metode.

![Gambar 2. Json Inline](img/02%20json_inline.PNG)

Gambar 2. Json Inline

Sayangnya, `jsonDecode()` mengembalikan `Map<String, dynamic>`, artinya kita tidak mengetahui tipe data dari nilai sampai runtime. Dengan pendekatan ini, kita kehilangan sebagian besar fitur statically typed, type safety, autocomplete, dan yang terpenting, compile-time exceptions atau pesan error ketika waktu compile. Itu artinya kode kita akan langsung menjadi lebih rawan dari kesalahan.

Misalnya, setiap kali kita mengakses name atau email, kita bisa saja langsung salah ketik. Kesalahan ketik ini tidak akan diketahui oleh kompiler karena JSON berada dalam struktur map. Error ini akan diketahui ketika runtime atau ketika aplikasi sedang berjalan.

![Gambar 3. jsonDecode tidak ada compile-time exception](img/03%20json_inline_error.PNG)

Gambar 3. jsonDecode tidak ada compile-time exception

### 2. Serializing JSON di dalam class model

Dalam menghadapi masalah yang disebutkan sebelumnya, saya akan memperkenalkan class model. Dalam contoh ini kita dapat menyebutnya `class User`. Di dalam `class User` ini, kita akan menemukan:

- Constructor `User.fromJson()`, untuk membuat instance User baru dari struktur Map.
- Metode `toJson()`, yang mengubah instance User menjadi Map.

Dengan pendekatan ini, pemanggilan kode dapat memiliki keamanan tipe (type safety), autocompletion untuk kolom name dan email, dan compile-time exceptions. Jadi kalau kita ada kesalahan ketik atau menganggap name dan email sebagai int, bukan String, aplikasi tidak akan bisa dikompilasi, sehingga tidak akan crash ketika runtime.

`user.dart`

```dart
class User {
  final String name;
  final String email;

  User(this.name, this.email);

  User.fromJson(Map<String, dynamic> json)
      : name = json['name'],
        email = json['email'];

  Map<String, dynamic> toJson() => {
        'name': name,
        'email': email,
      };
}
```

Tanggung jawab untuk decode sekarang pindah ke dalam class model. Dengan pendekatan baru ini, Anda dapat decode User dengan mudah.

![Gambar 4. jsonDecode dengan class model](img/04%20json_inline%20dengan%20class%20model.PNG)

Gambar 4. jsonDecode class model

Untuk encoding `User`, masukan objek `User` ke fungsi `jsonEncode()`. kita tidak perlu memanggil metode `toJson()`, karena `jsonEncode()` sudah melakukannya untuk kita.

Dengan pendekatan ini, pemanggilan kode tidak perlu mengkhawatirkan serialisasi JSON sama sekali. Namun, class model tetap harus kita perhatikan. Dalam aplikasi production, kita harus memastikan bahwa serialisasi berfungsi dengan baik. Dalam praktiknya, metode `User.fromJson()` dan `User.toJson()` harus memiliki unit test untuk memverifikasi serialization ini berjalan dengan benar.

## Serialization using Code Generator

**Code generator digunakan untuk project sedang sampai project besar.**

Serialisasi JSON dengan code generator artinya kita memiliki eksternal library yang menghasilkan encoding boilerplate untuk kita. Setelah menyiapkan class sesuai dengan aturan code generator, kita akan menjalankan file watcher yang akan menghasilkan kode dari class model tersebut. Misalnya, `json_serializable` dan `freezed` adalah jenis library yang dapat kita pakai.

Pendekatan ini sangat baik untuk proyek yang besar. Tidak diperlukan boilerplate secara manual, dan tidak ada kesalahan ketik saat mengakses file JSON pada waktu kompilasi. Kelemahan dari pembuatan kode adalah memerlukan beberapa pengaturan di awal. Juga, file yang dihasilkan mungkin memperlihatkan visual yang kurang enak di navigator proyek kita.

Untuk melihat contoh encoding JSON berbasis code generator, lihat Serialisasi JSON menggunakan library berikut.

### Setup json_serializable dalam project

Untuk menyertakan `json_serializable` dalam proyek kita, Anda memerlukan satu dependensi reguler, dan dua dependensi dev. Singkatnya, dependensi dev adalah dependensi yang tidak disertakan dalam sourcecode aplikasi kita, mereka hanya digunakan di lingkungan development.

Jalankan `flutter pub get` ke dalam folder root kita untuk membuat dependensi baru ini tersedia di project kita.

`pubspec.yaml`

```yaml
dependencies:
  json_annotation:

dev_dependencies:
  lints:
  test:
  build_runner:
  json_serializable:
```
