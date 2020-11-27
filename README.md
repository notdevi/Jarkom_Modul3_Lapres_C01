# Jarkom_Modul3_Lapres_C01
Praktikum Modul 3 Jaringan Komputer 2020

## NAMA ANGGOTA KELOMPOK :

**1. Devi Hainun Pasya (05111840000014)**

**2. Kevin Christian Hadinata (05111840000066)**

## PEMBAHASAN SOAL

### **Nomor 7**
Setelah melakukan pengaturan pada *proxy server* (Mojokerto), kita dapat melakukan fungsi ```htpasswd``` yang didapat melalui ```apache2-utils```.

Untuk kasus nomor 7 dan kelompok C01,
kami mengetikkan
```
htpasswd -c /etc/squid/passwd userta_c01
```
Setelah itu akan muncul tempat untuk melakukan *input password* dan kami isikan
```
inipassw0rdta_c01
```

Setelah itu, kami melakukan konfigurasi pada ```/etc/squid/squid.conf/``` dan mengetikkan
```
auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
auth_param basic children 5
auth_param basic realm Proxy
auth_param basic credentialsttl 2 hours
auth_param basic casesensitive on
acl USERS proxy_auth REQUIRED
http_access allow USERS
```

Setelah itu, lakukan restart pada squid dengan ```service squid restart``` maka halaman yang diakses menggunakan *proxy* akan membutuhkan *username* dan *password* untuk dapat diakses.

### **Nomor 8**
Dengan menggunakan ***Access Control Lists*** atau biasa disingkat ***ACL***, kita dapat mengatur waktu kapan *proxy* kita dapat mengakses suatu situs.

Pertama, buat konfigurasi di  ```/etc/squid/acl.conf```, lalu masukkan perintah yang diinginkan. *Syntax* yang digunakan antara lain:
```
acl {nama_acl) time {inisial hari} {jam dalam format 24H}
```
Sehingga, apabila kita mau membiarkan *proxy* dapat mengakses situs setiap hari ```Selasa-Rabu pukul 13.00-18.00```, kita dapat menuliskan:
```
acl AVAILABLE_WORKING time TW 13.00-18.00
```
**T** menandakan *Tuesday* dan **W** menandakan *Wednesday*.

Setelah itu, kita perlu melakukan konfigurasi kembali pada ```squid.conf``` dengan menambahkan
```
include /etc/squid/acl.conf

http_access allow AVAILABLE_WORKING
http_access deny all
```
Jangan lupa untuk melakukan ```service squid restart``` setelah selesai melakukan konfigurasi.

### **Nomor 9**
Karena *time* pada ACL berlaku format **h1:m1-h2:m2** dengan syarat ```h1<h2 dan m1<m2```, kita perlu memisahkan jam yang diperbolehkan untuk kasus nomor 9. Caranya dengan menambahkan:
```
acl BIMBINGAN time TWH 21:00-23:59
acl BIMBINGAN1 time WHF 00:00-09.00
```
pada ```acl.conf```. Setelah itu, kita tinggal menambahkan ***BIMBINGAN*** dan ***BIMBINGAN1*** pada ```squid.conf``` dengan menuliskan:
```
http_access allow AVAILABLE_WORKING BIMBINGAN BIMBINGAN1
```
Setelah itu, lakukan restart pada *squid*.

### **Nomor 10**
Untuk melakukan *redirect*, kita menggunakan fitur lain yang dimiliki oleh *squid*, yaitu **url_regex**.

Edit file ```/etc/squid/acl.conf``` dengan menambahkan:
```
acl redirect url_regex google.com
```
Setelah itu, kita tinggal menambahkan situs yang ingin kita tuju pada ```/etc/squid/squid.conf``` dengan cara:
```
http_access deny redirect
deny_info http://monta.if.its.ac.id/ redirect
```
Setelah itu lakukan restart pada *squid*.

### **Nomor 11**
Setelah melakukan ```wget 10.151.36.202/ERR_ACCESS_DENIED```, kita tinggal membuat direktori pada folder squid dengan cara
```
mkdir /etc/squid/pages/
```
lalu *copy file* yang barusan kita copy ke dalam folder tersebut:
```
cp -r ERR_ACCESS_DENIED /etc/squid/pages/
```

Setelah itu, kita tinggal menambahkan *folder* tersebut pada ```squid.conf``` dengan mengetikkan:
```
error_directory /etc/squid/pages
```
Setelah itu, lakukan restart *squid*.

### **Nomor 12**
Untuk membuat domain ***janganlupa-ta.c01.pw***, kita menggunakan langkah-langkah seperti dari modul sebelumnya, yaitu ```Domain Name Server```.

Buka dan *install* ```bind9``` pada UML ***Malang***. Setelah itu, pada ```/etc/bind/named.conf.local```, isikan:
```
zone "janganlupa-ta.c01.pw" {
	type master;
	file "/etc/bind/ta/janganlupa-ta.c01.pw";
};
```
Setelah itu, lakukan *copy* ```db.local``` dengan cara
```
cp /etc/bind/db.local /etc/bind/ta/janganlupa-ta.c01.pw
```
Langkah berikutnya, *edit file* tersebut dengan mengganti ```localhost``` menjadi ```janganlupa-ta.c01.pw``` dan memasukkan IP Mojokerto pada bagian
```
@       IN      A   10.151.77.19    ;IP MOJOKERTO
```
setelah itu lakukan *restart* pada ```bind9``` dan lakukan konfigurasi pada *proxy server* pada ***web browser*** dengan memasukkan ```janganlupa-ta.c01.pw```.

## Referensi
* https://github.com/arsitektur-jaringan-komputer/Modul-Jarkom/tree/modul-3/Proxy%20Server
* https://github.com/arsitektur-jaringan-komputer/Modul-Jarkom/tree/modul-2/DNS