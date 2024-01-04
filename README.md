# Flutter JSON Serialization dan REST API

Materi Mata Kuliah Pemrograman Mobile | Teknik Informatika UNISNU Jepara | Akhmad Khanif Zyen

---

> **Cara menggunakan modul ini:**
>
> 1. Login ke akun github anda.
> 2. Star ke repository ini.
> 3. Fork repository ini sehingga masuk ke repository di akun github masing-masing.
> 4. Clone project dari repository github masing-masing.
>    ```bash
>    git clone https://github.com/namauser/flutter-json.git
>    ```
> 5. Buat branch baru `dev` dan gunakan sebagai branch yang aktif.
>
>    ```bash
>    git checkout -b dev
>    ```
>
> 6. Mulai praktek, selesai praktek lakukan commit dengan label sesuai materi yang dikerjakan. Untuk project folder, anda bisa buat di dalam folder flutter-json dengan memilih Dart > New project > Console Application > Nama project "project_dart_json"
>
>    ```bash
>    git add .
>    git commit -m "add json serialization"
>    ```
>
> 7. Lakukan push ke repository github masing-masing
>    ```bash
>    git push -u origin dev
>    ```
> 8. Jika sudah selesai semua, kembali ke repository ini, kemudian masuk ke menu `issue`, tulis identitas diri (NIM, Nama) dan link repo github hasil dari fork project ini.
> 9. Penilaian berdasarkan commit di branch `dev` pada repo akun github masing-masing.

Daftar Isi:

1. [Apa itu REST API](#apa-itu-rest-api)
2. [Apa itu API](#apa-itu-api)
   1. [Client](#client)
   2. [Resources](#resources)
3. [Apa itu REST](#apa-itu-rest)
4. [Manfaat REST API](#manfaat-rest-api)
5. [Isi Permintaan (Request) REST API](#isi-permintaan-request-rest-api)
   1. [Unique resource identifier](#1-unique-resource-identifier)
   2. [Method](#2-method)
   3. [HTTP Header](#3-http-header)
   4. [Data](#4-data)
   5. [Parameter](#5-parameter)
6. [Metode autentikasi REST API](#metode-autentikasi-rest-api)
   1. [HTTP Authentication](#http-authentication)
7. [Isi Response REST API](#isi-response-server-rest-api)
   1. [Status Code](#status-code)
   2. [Response Body](#response-body)
   3. [Response Header](#response-header)
8. [Materi Selanjutnya Json & Serialization](#materi-selanjutnya-json--serialization)

## Apa itu REST API?

REST API adalah antarmuka (interface) yang digunakan dua sistem komputer untuk bertukar informasi secara aman melalui internet. Sebagian besar aplikasi bisnis harus berkomunikasi dengan aplikasi internal dan pihak ketiga lainnya untuk melakukan berbagai kegiatan. Misalnya, untuk pembayaran courses joints academy, system joints akan membuat penagihan dengan mengirim request ke payment gateway dengan data user, setelah itu system joints akan menerima kode bayar yang dikirim ke user joints via joints apps, setelah user berhasil payment, system payment gateway akan mengirimkan data ke system joints dengan data user joints telah berhasil melakukan pembayaran, kemudian system joints otomatis mengupdate data pembayaran user. RESTful API mendukung pertukaran informasi ini karena mengikuti standar komunikasi perangkat lunak yang aman, andal, dan efisien.

## Apa itu API ?

Application programming interface (API) menentukan aturan yang harus Anda ikuti untuk berkomunikasi dengan sistem perangkat lunak lain. Pengembang memaparkan atau membuat API sehingga aplikasi lain dapat berkomunikasi dengan aplikasi mereka secara terprogram. Misalnya, aplikasi system payment gateway sudah mempunyai API untuk membuat tagihan otomatis, maka system joints bisa mengakses API tersebut untuk membuatkan tagihan untuk user joints melalui apps joints. Disini backend joints pun perlu menyiapkan api untuk menerima request dari joints apps. Jadi prosesnya, joints apps request ke API backend, backend(system joints) request ke API payment gateway.

**Kita dapat menganggap API sebagai gerbang antara Client dan Resources di server.**

### Client

Client adalah pengguna yang ingin mengakses informasi dari server. Client dapat berupa orang atau sistem software yang menggunakan API. Misalnya, user joints dengan menggunakan joints apps dapat mengakses data kursus dari sistem backend joints. joints apps adalah client aplikasi.

### Resources

Resources (sumber daya) adalah informasi yang diberikan oleh aplikasi kepada client. Resources dapat berupa gambar, video, teks, angka, atau informasi apa pun. Mesin yang memberikan resources ke client disebut server. Server menggunakan API untuk berbagi resources dan menyediakan layanan sambil menjaga keamanan, kontrol, dan autentikasi. Selain itu, API membantu server menentukan client mana yang mendapatkan akses ke internal resources tertentu.

## Apa itu REST?

Representational State Transfer (REST) adalah arsitektur perangkat lunak yang menerapkan persyaratan tentang cara kerja API. REST awalnya dibuat sebagai pedoman untuk mengatur komunikasi pada jaringan yang kompleks seperti internet. Kita dapat menggunakan arsitektur berbasis REST untuk mendukung komunikasi berperforma tinggi dan andal dalam skala besar. Anda dapat dengan mudah mengimplementasikan dan memodifikasinya, menghadirkan visibilitas dan portabilitas lintas platform ke sistem API manapun. API yang mengikuti gaya arsitektur REST disebut REST API.

Berikut ini adalah beberapa prinsip gaya arsitektur REST:

1. Antarmuka seragam (uniform interface)

   Antarmuka yang seragam sangat penting untuk desain layanan REST apa pun. Ini menunjukkan bahwa server mentransfer informasi dalam format standar. resource yang diformat disebut representasi dalam REST. Format ini bisa berbeda dari representasi internal resource pada aplikasi server. Misalnya, server dapat menyimpan data sebagai teks tetapi mengirimkannya dalam format representasi JSON.

2. Statelessness

   Dalam arsitektur REST, Statelessness mengacu pada metode komunikasi di mana server menyelesaikan setiap permintaan client secara independen dari semua permintaan sebelumnya. Client dapat request resource kapan saja, dan setiap request tidak memiliki state khusus, atau dengan kata lain diisolasi dari request lainnya. Batasan desain REST API ini memberi pemahaman bahwa server dapat sepenuhnya memahami dan memenuhi request setiap saat.

3. Layered System

   Dalam arsitektur layer system, client dapat terhubung ke perantara resmi lainnya antara client dan server, dan masih akan menerima response dari server. Server juga dapat meneruskan request ke server lain. Anda dapat mendesain layanan REST Anda untuk berjalan di beberapa server dengan banyak lapisan/layer seperti layer keamanan, layer aplikasi, dan layer bisnis logic, bekerja sama untuk memenuhi request dari client. Layer layer ini tidak terlihat oleh client.

4. Kemampuan cache (Cacheability)

   Layanan REST mendukung caching, yaitu proses menyimpan beberapa respons pada client atau perantara untuk meningkatkan waktu respons server. Misalnya, Anda mengunjungi server yang memiliki data yang sama di setiap requestnya. Setiap kali Anda mengunjungi api tersebut, server harus mengirimkan ulang data. Untuk menghindari hal ini, klien meng-cache atau menyimpan data ini setelah respons pertama dan kemudian menggunakan data langsung dari cache. Layanan REST mengontrol caching dengan menggunakan respons API yang mendefinisikan dirinya apakah dapat di-cache atau tidak dapat di-cache.

5. Kode sesuai permintaan (code on demand)

   Dalam gaya arsitektur REST, server dapat memperluas atau menyesuaikan fungsionalitas klien untuk sementara waktu dengan mentransfer kode pemrograman perangkat lunak ke klien. Misalnya, saat Anda mengisi formulir pendaftaran di situs web mana pun, browser Anda segera menyoroti kesalahan apa pun yang Anda buat, seperti nomor telepon yang salah. Itu dapat dilakukan karena kode yang dikirim oleh server ke client.

## Manfaat REST API

1. Skalabilitas

   Sistem yang mengimplementasikan REST API dapat berkembang secara efisien karena REST mengoptimalkan interaksi klien-server. statelessness menghapus beban server karena server tidak harus menyimpan informasi permintaan klien sebelumnya. Caching yang dikelola dengan baik sebagian atau seluruhnya menghilangkan beberapa interaksi klien-server. Semua fitur ini mendukung skalabilitas tanpa menyebabkan hambatan komunikasi yang mengurangi kinerja.

2. Fleksibilitas

   Layanan REST mendukung pemisahan total klien-server. Mereka menyederhanakan dan memisahkan berbagai komponen server sehingga setiap bagian dapat berkembang secara mandiri. Perubahan platform atau teknologi pada aplikasi server tidak mempengaruhi aplikasi client. Kemampuan untuk melapisi fungsi aplikasi meningkatkan fleksibilitas lebih jauh lagi. Misalnya, pengembang dapat membuat perubahan pada layer database tanpa menulis ulang logic aplikasi.

3. Independence

   REST API tidak bergantung pada teknologi yang digunakan. Anda dapat menulis aplikasi client dan server dengan berbagai bahasa pemrograman tanpa mempengaruhi desain API. Anda juga dapat mengubah teknologi di kedua sisi tanpa mempengaruhi komunikasi. Contohnya server dibuat menggunakan bahasa python, aplikasi client dibuat menggunakan flutter.

## Isi Permintaan (Request) REST API

REST API memerlukan request untuk membawa komponen berikut:

### 1. Unique resource identifier

Server mengidentifikasi setiap resource dengan pengidentifikasi resource yang unik. Untuk layanan REST, server biasanya melakukan identifikasi resource dengan menggunakan Uniform Resource Locator (URL). URL menentukan jalur ke resource. URL mirip dengan alamat situs web yang Anda masukkan ke browser untuk mengunjungi halaman web. URL juga disebut sebagai request endpoint dan dengan jelas menentukan ke server mana yang dibutuhkan klien.

### 2. Method

Developer sering mengimplementasikan REST API dengan menggunakan Hypertext Transfer Protocol (HTTP). Method HTTP memberi tahu server apa yang perlu dilakukannya terhadap resource. Berikut ini adalah empat methodHTTP secara umum:

1. GET:

   Client menggunakan GET untuk mengakses resource yang terletak di URL yang ditentukan di server. Mereka dapat meng-cache request GET dan request parameter dalam request REST API untuk menginstruksikan server memfilter data sebelum dikirim ke client.

2. POST:

   Client menggunakan POST untuk mengirim data ke server. Mereka menyertakan representasi data dengan permintaan. Mengirim permintaan POST yang sama berkali-kali memiliki efek samping membuat resource yang sama berkali-kali.

3. PUT:

   Client menggunakan PUT untuk memperbarui resource yang ada di server. Tidak seperti POST, mengirimkan permintaan PUT yang sama beberapa kali dalam layanan REST memberikan hasil yang sama.

4. DELETE:

   Client menggunakan permintaan DELETE untuk menghapus resource. Permintaan DELETE dapat mengubah status server. Namun, jika pengguna tidak memiliki autentikasi yang sesuai, permintaan akan gagal.

### 3. HTTP Header:

Request headers adalah metadata yang dipertukarkan antara klien dan server.Misalnya, header request menunjukkan format request dan response, memberikan informasi tentang status request, dan sebagainya.

### 4. Data:

Request REST API mungkin menyertakan data untuk POST, PUT, dan method HTTP lainnya agar berhasil.

### 5. Parameter:

Request REST API dapat menyertakan parameter yang memberi server lebih banyak detail tentang apa yang perlu dilakukan. Berikut ini adalah beberapa jenis parameter yang ada dan perbedaannya:

- Path parameter yang menentukan detail URL.
- Query parameter yang meminta lebih banyak informasi tentang sumber daya.
- Cookie parameter yang mengautentikasi klien dengan cepat.

## Metode autentikasi REST API?

Layanan REST harus mengautentikasi permintaan sebelum dapat mengirim respons. Otentikasi adalah proses memverifikasi identitas. Misalnya, Anda dapat membuktikan identitas Anda dengan menunjukkan KTP atau SIM. Demikian pula, klien layanan REST harus membuktikan identitas mereka ke server untuk membangun kepercayaan.

### HTTP authentication:

HTTP menentukan beberapa skema autentikasi yang dapat Anda gunakan secara langsung saat mengimplementasikan REST API. Berikut ini adalah dua dari skema ini:

1. Basic authentication:

   Dalam basic authentication, klien mengirimkan nama pengguna dan kata sandi di header permintaan. Itu mengencodekan dengan base64, yang merupakan teknik pengkodean yang mengubah pasangan menjadi satu set 64 karakter untuk transmisi yang aman.

2. Bearer authentication:

   Bearer auth istilah ini mengacu pada proses pemberian kontrol akses ke pembawa token. Token pembawa biasanya berupa string karakter terenkripsi yang dihasilkan server sebagai tanggapan atas permintaan login. Klien mengirimkan token di header permintaan untuk mengakses resource.

## Isi Response Server REST API

Prinsip REST mengharuskan respons server untuk memuat komponen utama berikut:

### Status code

Status code berisi kode status tiga digit yang mengomunikasikan keberhasilan atau kegagalan permintaan. Misalnya, kode 2XX menunjukkan keberhasilan, tetapi kode 4XX dan 5XX menunjukkan kesalahan. Kode 3XX menunjukkan pengalihan URL. Berikut ini adalah beberapa status code yang umum:

- 200: Tanggapan sukses umum
- 201: Tanggapan keberhasilan metode POST
- 400: Permintaan salah yang tidak dapat diproses oleh server
- 404: Resource tidak ditemukan

### Response Body:

Respon body berisi representasi resource. Server memilih format representasi yang sesuai berdasarkan isi request header. Client dapat meminta informasi dalam format JSON, yang menentukan bagaimana data ditulis dalam teks biasa. Misalnya, jika client request name dan age seseorang bernama John, server mengembalikan representasi JSON sebagai berikut:

`'{"name":"John", "age":30}'`

### Response Header:

Respons juga berisi header atau metadata tentang response. Mereka memberikan lebih banyak konteks tentang respons dan menyertakan informasi seperti server, penyandian, tanggal, dan tipe konten.

**Setelah memahami REST API, sekarang waktunya kita belajar bagaimana flutter berkoneksi dengan api menggunakan transmisi json.**

# [Materi Selanjutnya JSON & Serialization](json.md)
