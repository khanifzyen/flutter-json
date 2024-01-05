# Flutter Networking

> Daftar Isi:
>
> 1. [Perkenalan](#perkenalan)
>    1. [Thunder Client](#thunder-client)
> 2. [Mengambil Data dari Internet/GET](#mengambil-data-dari-internet-get)
>    1. [Test Api Menggunakan Thunder Client](#test-api-menggunakan-thunder-client)
>    2. [Menambahkan Package http](#menambahkan-package-http)
>    3. [Konversi Response Menjadi Object Dart](#konversi-response-menjadi-object-dart)
>    4. [Ubah http.Response menjadi Album](#ubah-httpresponse-menjadi-album)
>    5. [Ambil Data](#ambil-data)
>    6. [Tampilkan Data](#tampilkan-data)
>    7. [Mengapa fetchAlbum() dipanggil di initState()](#mengapa-fetchalbum-dipanggil-di-initstate)
> 3. [Mengirim Data ke Internet/POST](#mengirim-data-ke-internetpost)
>    1. [Ubah http.Response ke Album](#ubah-httpresponse-ke-album)
>    2. [Mendapatkan title dari inputan user](#mendapatkan-title-dari-inputan-user)
>    3. [Menampilkan response ke layar](#menampilkan-response-ke-layar)
> 4. [Update Data Melalui Internet/PUT](#update-data-melalui-internetput)
>    1. [Membuat UI untuk Update Data](#membuat-ui-untuk-update-data)
> 5. [Hapus Data Melalui Internet/DELETE](#hapus-data-lewat-internet)
>    1. [Membuat UI untuk Delete Data](#membuat-ui-untuk-delete-data)

## Perkenalan

Salah satu fitur yang ditawarkan oleh Flutter adalah kemampuannya untuk melakukan networking, yaitu proses pertukaran data melalui jaringan (biasanya internet).

Untuk melakukan networking menggunakan Flutter, kamu bisa menggunakan package bernama http, yang sudah disediakan oleh Flutter. Package ini memungkinkan kamu untuk melakukan berbagai operasi networking, seperti mengirimkan HTTP GET, POST, PUT, DELETE.

Selain package http, ada package lain yang bernama dio yang merupakan sebuah library HTTP client yang lebih kuat dan fleksibel dibandingkan dengan package http. Package dio ini cocok untuk melakukan operasi networking yang lebih kompleks di Flutter. Namun untuk topic kali ini, kita hanya akan menggunakan package http

### Thunder Client

Untuk mempermudah dalam integrasi API kedalam flutter, akan lebih mudah ketika kita sudah dapat melihat url api, request body, dan response body nya. Untuk itu kita perlu tools/software lain untuk melakukan pengecekan Rest API nya. Disini saya akan memperkenalkan teman teman dengan software bernama Thunder Client, Cari extensionnya di VSCode, kemudian Install. Nanti tampilannya seperti gambar disamping :

![Gambar 8 Tampilan Thunder Client](img/08%20Tampilan%20Thunder%20Client.PNG)

Gambar 8. Tampilan Thunder Client

Alternative Thunder Client temen-temen bisa menggunakan POSTMAN.

## Mengambil data dari internet (GET)

Untuk mengambil data dari internet, kita dapat menggunakan method GET. Metode GET adalah salah satu metode yang disediakan oleh package http ini, yang digunakan untuk mengirimkan permintaan ke server API untuk mengambil data.
Sebagai contoh kita akan mengambil data dari API URL: https://jsonplaceholder.typicode.com/albums/1

Pertama yang perlu kita lakukan adalah mengecek dulu api ini di thunderclient, api ini berkerja dengan benar apa tidak.

### Test API menggunakan Thunder Client

Disini saya cek dulu url api nya di aplikasi thunder client untuk mendapatkan data dari api url yang sudah disiapkan oleh team backend. Untuk keterangannya sudah saya kasih angka:

1. Klik New Request untuk membuat request api baru
2. Method, contoh method GET
3. Masukkan URL, tadi untuk contoh mengambil dari link typicode diatas
4. Klik button SEND
5. Response body berupa json format dengan status code, berhasil 200.

![Gambar 9 Request Pertama](img/09%20Request%20Pertama.PNG)

Gambar 9. Request Pertama

### Menambahkan package http

Setelah kita tahu cek menggunakan postman dan normal, saatnya kita masuk ke kode flutter. Pertama kita buat dulu project nya dengan cara `flutter create project_rest_api`

Setelah itu kita add package http dengan cara `flutter pub add http` Jangan lupa untuk menambahkan configurasi di yang berada difolder `android/app/src/main/Androidmanifest.xml`

```xml
<uses-permission android:name="android.permission.INTERNET"/>
```

![Gambar 10 Menambahkan permission internet](img/10%20Menambahkan%20permission%20internet.PNG)

Gambar 10. Menambahkan permission internet

Pertama kita import dulu package http nya seperti pada baris pertama. Setelah itu kita dapat membuat `class NetworkManager` untuk menampung function http method yang akan kita gunakan. Disini saya mencontohkan membuat `function fetchAlbum()`.

```dart
import 'package:http/http.dart' as http;

class NetworkManager {
  Future<http.Response> fetchAlbum() {
    return http.get(Uri.parse('https://jsonplaceholder.typicode.com/albums/1'));
  }
}
```

Metode `http.get()` mengembalikan Future yang berisi Response.

- Future adalah class core Dart untuk bekerja dengan operasi async. Objek Future mewakili potensi nilai atau kesalahan yang akan tersedia di masa mendatang.
- class `http.Response` berisi data yang diterima dari panggilan http yang berhasil.

### Konversi response menjadi object Dart

Meskipun mudah untuk membuat request network, namu bekerja dengan Future<http.Response> yang mentah sangat tidak nyaman. Untuk membuat ini lebih mudah kita handle, yuk kita ubah `http.Response` menjadi object Dart dengan class json serialization seperti topik sebelumnya.

Pertama, buat `class Album` yang berisi data hasil dari request API GET. Mengonversi JSON secara manual hanyalah salah satu opsi. Untuk informasi lebih dalam bisa kembali ke topic JSON Serialization. Hasil class Album dapat dilihat seperti pada kode berikut.

```dart
class Album {
  final int userId;
  final int id;
  final String title;

  Album({
    required this.userId,
    required this.id,
    required this.title,
  });

  factory Album.fromJson(Map<String, dynamic> json) {
    return Album(
      userId: json['userId'],
      id: json['id'],
      title: json['title'],
    );
  }
}
```

### Ubah http.Response menjadi Album

Sekarang, gunakan langkah-langkah berikut untuk memperbarui fungsi `fetchAlbum()` untuk mengembalikan `Future<Album>`

Ubah respons body menjadi JSON Map dengan paket `dart:convert`. Jika server mengembalikan respons OK dengan kode status 200, konversi JSON map menjadi Album menggunakan metode factory fromJson().

Jika server tidak mengembalikan respons OK dengan kode status 200, maka berikan exception.(Bahkan dalam kasus respons server "404 Not Found"). Jangan kembalikan null.

```dart
import 'package:http/http.dart' as http;
import 'album.dart';
import 'dart:convert';

class NetworkManager {
  Future<Album> fetchAlbum() async {
    final response = await http
        .get(Uri.parse('https://jsonplaceholder.typicode.com/albums/1'));

    if (response.statusCode == 200) {
      return Album.fromJson(jsonDecode(response.body));
    } else {
      throw Exception("Failed to load Album");
    }
  }
}
```

### Ambil Data

Panggil metode `fetchAlbum()` di dalam metode `initState()`.
Metode `initState()` dipanggil hanya sekali dalam siklus stateful widget ketika widget ini dipanggil.

```dart
...
class _MyHomePageState extends State<MyHomePage> {
  late Future<Album> album;

  @override
  void initState() {
    super.initState();
    album = NetworkManager().fetchAlbum();
  }
...
```

### Tampilkan Data

Untuk menampilkan data di layar, gunakan widget `FutureBuilder`. Widget `FutureBuilder` memudahkan untuk bekerja dengan sumber data async. Anda harus menyediakan dua parameter:

- Future yang ingin Anda kerjakan. Dalam hal ini, future dikembalikan dari fungsi `fetchAlbum()`.
- `builder` function memberi tahu Flutter apa yang harus dirender, bergantung pada status dari future: loading, success, atau error.

Perhatikan bahwa `snapshot.hasData` hanya mengembalikan true saat `snapshot` berisi nilai data bukan null. Karena `fetchAlbum` hanya dapat mengembalikan nilai non-null, fungsi tersebut harus mengarahkan exception ketika terjadi error.

```dart
...
      body: FutureBuilder(
        future: album,
        builder: (context, snapshot) {
          if (snapshot.hasData) {
            return Center(child: Text(snapshot.data!.title));
          } else if (snapshot.hasError) {
            return Text('${snapshot.error}');
          }
          return const Center(
            child: CircularProgressIndicator(),
          );
        },
      ),
...
```

### Mengapa fetchAlbum() dipanggil di initState()

Meskipun aman, tidak disarankan untuk melakukan panggilan API dalam metode `build()`. Flutter memanggil metode `build()` setiap kali perlu mengubah apa pun dalam tampilan, dan ini sering terjadi secara mengejutkan. Metode `fetchAlbum()`, jika ditempatkan di dalam `build()`, berulang setiap kali dipanggil pada setiap rebuild yang menyebabkan aplikasi melambat.

Menyimpan hasil `fetchAlbum()` dalam variabel status memastikan bahwa future dijalankan hanya sekali dan kemudian di-cache untuk rebuild selanjutnya. Hasil app ketika dirunning akan seperti dibawah. Ini data sudah ambil dari REST API.

![Gambar 11 menampilkan data dari rest api](img/11%20menampilkan%20data%20dari%20rest%20api.png)

Gambar 11. Menampilkan data dari rest api

## Mengirim data ke internet/POST

Sebagian besar aplikasi memerlukan pengiriman data melalui internet, dan kita dapat menggunakan package http. HTTP Method POST adalah salah satu metode yang ada dalam HTTP (Hypertext Transfer Protocol), yang digunakan untuk mengirim data dari aplikasi ke server. Dengan menggunakan package http, kita dapat mengirim data ke server dengan menggunakan HTTP Method POST.
Berikut langkah langkahnya :

Pertama tambahkan http package dan config internet permission di
AndroidManifest.xml seperti pada bab http get.

Setelah itu kita dapat menuliskan kode untuk mengirimkan judul album ke url api: https://jsonplaceholder.typicode.com/albums Dengan http.post seperti potongan kode dibawah:

`network_manager.dart`

```dart
...
  Future<http.Response> createAlbum(String title) async {
    return http.post(
      Uri.parse('https://jsonplaceholder.typicode.com/albums'),
      headers: <String, String>{
        'Content-Type': 'Application/json; charset=UTF-8',
      },
      body: jsonEncode(<String, String>{
        'title': title,
      }),
    );
  }
...
```

Metode http.post() mengembalikan Future yang berisi Response.

- `Future` adalah class core Dart untuk bekerja dengan operasi asinkron. Objek future mewakili potensi nilai atau kesalahan yang akan tersedia di masa mendatang.
- class `http.Response` berisi data yang diterima dari panggilan http ketika berhasil.
- Metode `createAlbum()` mengambil argument title lalu dikirim ke server untuk membuat Album.

### Ubah http.Response ke Album

Gunakan langkah-langkah berikut untuk memperbarui fungsi `createAlbum()` untuk mengembalikan `Future<Album>`

- Jika server mengembalikan respons `CREATED` dengan kode status 201, konversi JSON map menjadi Album menggunakan metode `factory fromJson()`.
- Jika server tidak mengembalikan respons `CREATED` dengan kode status 201, maka masukan ke exception. (Bahkan dalam kasus
  respons server "404 Not Found".)

`network_manager.dart`

```dart
...
  Future<Album> createAlbum(String title) async {
    final response = await http.post(
      Uri.parse('https://jsonplaceholder.typicode.com/albums'),
      headers: <String, String>{
        'Content-Type': 'Application/json; charset=UTF-8',
      },
      body: jsonEncode(<String, String>{
        'title': title,
      }),
    );

    if (response.statusCode == 201) {
      return Album.fromJson(jsonDecode(response.body));
    } else {
      throw Exception('Failed to create album');
    }
  }
...
```

### Mendapatkan title dari inputan user

Selanjutnya, buat `TextField` untuk memasukkan `title` dan `ElevatedButton` untuk mengirim data ke server. Juga tentukan `TextEditingController` untuk membaca input pengguna dari `TextField`. Saat `ElevatedButton` ditekan, `album` diset ke nilai yang dikembalikan oleh metode `createAlbum()`.

```dart
...
class _MyHomePageState extends State<MyHomePage> {
  late Future<Album> album;
  final TextEditingController _controller = TextEditingController();
  ...
        body: Column(
        mainAxisAlignment: MainAxisAlignment.start,
        children: [
          TextField(
            controller: _controller,
            decoration: const InputDecoration(labelText: 'Enter Title'),
          ),
          const SizedBox(height: 5),
          ElevatedButton(
            onPressed: () {
              setState(() {
                album = NetworkManager().createAlbum(_controller.text);
              });
            },
            child: const Text('Create Data'),
          ),
          const SizedBox(height: 10),
          FutureBuilder(
            ...
```

### Menampilkan response ke layar

Restart tampilan layar, maka teks awal akan muncul. Selanjutnya isikan textfield, kemudian klik Create Data, maka akan muncul error `Type 'Null' is not a subtype of type 'int'`.

![Gambar 12 post data masih error](img/12%20post%20data%20masih%20error.png)

Gambar 12. Http post data masih error

Hal ini dikarenakan pada class model user, disyaratkan pada constructor kalau `userId`, `id` wajib diisi. Solusinya adalah dengan mengisi parameter constructor tersebut, atau pada class user model disyaratkan untuk `id` dan `userid` boleh null.

Solusi 1: `network_manager.dart`

```dart
...
  Future<Album> createAlbum(String title) async {
    ...
      body: jsonEncode(<String, dynamic>{
        'title': title,
        'userId': 1,
        'id' : 101,
      }),
    );
...
```

Solusi 2: `album.dart`

```dart
class Album {
  int? userId;
  int? id;
  final String title;

  Album({
    this.userId,
    this.id,
    required this.title,
  });
  ...
```

![Gambar 13 post data berhasil](img/13%20post%20data%20berhasil.png)

Gambar 13. Post data berhasil

## Update data melalui internet/PUT

Kebanyakan aplikasi memerlukan fungsi update data melalui jaringan internet dan http package menyediakan function itu dengan http put.

HTTP Method Put adalah sebuah metode yang digunakan untuk mengupdate data pada sebuah server dengan menggunakan HTTP. Metode ini biasanya digunakan untuk mengubah data yang sudah ada di server dengan data baru yang telah diubah oleh pengguna.

Melanjutkan study case sebelumnya get album create album. Sekarang kita coba untuk update album. Langkah langkahnya adalah sebagai berikut

- Function `updateAlbum` mempunyai 2 argument yaitu `id` yang nantinya untuk parameter `album` yang akan di update, serta argument `title` untuk pengganti dari `title` yang dimiliki `id` tersebut.
- Pada api url, untuk `id` kita menggunakan `id` dari variable sehingga dapat dinamis untuk mengganti album-album yang ada.
- Setelah itu ketika update success dengan statuscode 200 maka akan mengembalikan response body yang sudah dikonversi ke dart model album
- Ketika terjadi error maka akan diteruskan ke class exception

`network_manager.dart`

```dart
...
  Future<Album> updateAlbum(int id, String title) async {
    final response = await http.put(
      Uri.parse('https://jsonplaceholder.typicode.com/albums/$id'),
      headers: <String, String>{
        'Content-Type': 'application/json; charset=UTF-8',
      },
      body: jsonEncode(
        <String, String>{
          'title': title,
        },
      ),
    );

    if (response.statusCode == 200) {
      return Album.fromJson(jsonDecode(response.body));
    } else {
      throw Exception('Failed to update album');
    }
  }
...
```

### Membuat UI untuk update data

Karena inputan diambilkan dari TextField yang sama, maka tinggal membuat tombol Update Data. Buat tombol tersebut disamping Create Data

```dart
...
      body: Column(
        ...
        children: [
          TextField(...),
          const SizedBox(height: 5),
          Row(
            ...
            children: [
              ElevatedButton(
                ...
                child: const Text('Create Data'),
              ),
              const SizedBox(width: 10),
              ElevatedButton(
                  onPressed: () {
                    setState(() {
                      album = NetworkManager().updateAlbum(1, _controller.text);
                    });
                  },
                  child: const Text('Update Data')),
            ],
          ),
          const SizedBox(height: 10),
          FutureBuilder(
...
```

![Gambar 14 update data berhasil](img/14%20update%20data%20berhasil.png)

Gambar 14. Update data berhasil

## Hapus Data Lewat Internet

HTTP method DELETE adalah salah satu metode yang tersedia dalam
protokol HTTP, yang digunakan untuk menghapus suatu data dari sebuah API. Jika Anda menggunakan paket HTTP di Flutter, Anda dapat menggunakan method DELETE untuk mengirimkan permintaan penghapusan data ke sebuah API yang tersedia melalui protokol HTTP. Contoh penggunaan HTTP Method DELETE dalam Flutter adalah sebagai berikut.

Meneruskan study case api album. Disini kita akan belajar bagaimana delete data album yang sudah pernah kita create.

- Pertama buat function `deleteAlbum` dengan argument `id`, lalu kita dapat menggunakan http delete dan memasukan argument `id` tadi ke dalam parameter url api untuk menghapus data album yang ada di server.
- Setelah itu jika response status code nya 200makan kita akan mengembalikan data album namun dengan data yang kosong karena dari api kita akan menerima json kosong yang artinya proses delete berhasil, begitu juga kalau terjadi gagal proses maka akan diteruskan ke class exception.

`network_manager.dart`

```dart
...
  Future<Album> deleteAlbum(int id) async {
    final response = await http.delete(
        Uri.parse('https://jsonplaceholder.typicode.com/albums/$id'),
        headers: <String, String>{
          'Content-Type': 'application/json; charset=UTF-8',
        });

    if (response.statusCode == 200) {
      // Jika server mengembalikan respons 200 OK,
      // maka parsing JSON. Setelah menghapus
      // anda akan mendapat kembalian respon json kosong `{}`.
      // Jangan kembalikan `null`, jika mengembalikan null `snapshot.hasData`
      // akan selalu mengembalikan false pada `FutureBuilder`
      return Album.fromJson(jsonDecode(response.body));
    } else {
      throw Exception('Failed to delete album');
    }
  }
...
```

### Membuat UI untuk delete data

Karena delete tidak membutuhkan TextField, maka tinggal membuat tombol Data Data. Buat tombol tersebut disamping Update Data, dengan menerapkan komen diatas.

```dart
...
              ElevatedButton(
                onPressed: () {
                  setState(() {
                    album = NetworkManager().deleteAlbum(1);
                  });
                },
                child: const Text('Delete Data'),
              ),
            ],
          ),
          const SizedBox(height: 10),
          FutureBuilder(
            future: album,
            builder: (context, snapshot) {
              if (snapshot.hasData) {
                return Center(child: Text(snapshot.data?.title ?? 'Deleted'));
              } else if (snapshot.hasError) {
                return Text('${snapshot.error}');
              }
              return const Center(
                child: CircularProgressIndicator(),
              );
            },
          ),
...
```

Ada sedikit perubahan pada class model Album, yaitu menjadikan property `title` boleh null.

```dart
class Album {
  int? userId;
  int? id;
  String? title;

  Album({
    this.userId,
    this.id,
    this.title,
  });
  ...
```

![Gambar 15 delete data berhasil](img/15%20delete%20data%20berhasil.png)

Gambar 15. Delete data berhasil
