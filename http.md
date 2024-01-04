# Flutter Networking

## Perkenalan

Salah satu fitur yang ditawarkan oleh Flutter adalah emampuannya untuk melakukan networking, yaitu proses pertukaran data melalui jaringan (biasanya internet).

Untuk melakukan networking menggunakan Flutter, kamu bisa menggunakan package bernama http, yang sudah disediakan oleh Flutter. Package ini memungkinkan kamu untuk melakukan berbagai operasi networking, seperti mengirimkan HTTP GET, POST, PUT, DELETE.

Selain package http, ada package lain yang bernama dio yang merupakan sebuah library HTTP client yang lebih kuat dan fleksibel dibandingkan dengan package http. Package dio ini cocok untuk melakukan operasi networking yang lebih kompleks di Flutter. Namun untuk topic kali ini, kita hanya akan menggunakan package http

## Thunder Client

Untuk mempermudah dalam integrasi API kedalam flutter, akan lebih mudah ketika kita sudah dapat melihat url api, request body, dan response body nya. Untuk itu kita perlu tools/software lain untuk melakukan pengecekan Rest API nya. Disini saya akan memperkenalkan teman teman dengan software bernama Thunder Client, Cari extensionnya di VSCode, kemudian Install. Nanti tampilannya seperti gambar disamping :

![Gambar 8 Tampilan Thunder Client](img/08%20Tampilan%20Thunder%20Client.PNG)

Gambar 8. Tampilan Thunder Client

Alternative Thunder Client temen-temen bisa menggunakan POSTMAN.

## Mengambil data dari internet

Untuk mengambil data dari internet, kita dapat menggunakan method GET. Metode GET adalah salah satu metode yang disediakan oleh package http ini, yang digunakan untuk mengirimkan permintaan ke server API untuk mengambil data.
Sebagai contoh kita akan mengambil data dari API URL: https://jsonplaceholder.typicode.com/albums/1

Pertama yang perlu kita lakukan adalah mengecek dulu api ini di thunderclient, api ini berkerja dengan benar apa tidak.

![Gambar 9 Request Pertama](img/09%20Request%20Pertama.PNG)

Gambar 9. Request Pertama

## Test API menggunakan POSTMAN

## Menambahkan package http

## Konversi response menjadi object Dart

## Ubah http.Response menjadi Album

## Ambil Data

## Tampilkan Data

## Mengapa fetchAlbum() dipanggil di initState()

## Mengirim data ke internet

## Convert response dari http.Response ke Album

## Mendapatkan title dari inputan user

## Menampilkan response ke layar
