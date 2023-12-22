# Flutter JSON

JSON (JavaScript Object Notation) adalah format pertukaran data yang ringan. Sangat mudah bagi manusia untuk membaca dan menulisnya. Mudah bagi mesin untuk memprosesnya dan membuatnya. Format ini didasarkan pada Standar Bahasa Pemrograman JavaScript ECMA-262 Edisi ke-3 - Desember 1999. JSON adalah format teks yang sepenuhnya terbebas dari bahasa pemrograman apapun, akan tetapi dalam prakteknya json ini menggunakan konvensi yang akrab dengan bahasa pemrograman dari keluarga bahasa C, termasuk C, C++, C#, Java, JavaScript, Perl, Python, dan masih banyak lainnya. Kemudahan dan keunggulannya ini menjadikan JSON sebagai bahasa pertukaran data yang ideal dan terkenal untuk saat ini.

![Gambar 01. Struktur JSON dibandingkan List/Array pada umumnya](img/01%20struktur%20json.PNG)

Gambar 01. Struktur JSON dibandingkan List/Array pada umumnya

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

Serialisasi JSON di Flutter sangatlah sederhana. Flutter memiliki library `dart:convert` bawaan yang menyediakan fungsi encoder dan decoder JSON secara langsung.

Contoh JSON berikut mengimplementasikan secara sederhana model `user`. Dengan `dart:convert`, kita dapat membuat serialize model JSON ini dengan dua cara:

1. Serializing JSON secara inline
2. Serializing JSON didalam class model
