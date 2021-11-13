# Jarkom-Modul-3-E02-2021

# 1. Mengatur EniesLobby sebagai DNS Server, Jipangu sebagai DHCP Server, Water7 sebagai Proxy Server.

Berdasarkan soal di atas maka akan dilakukan beberapa konfigurasi pada node EniesLobby, Jipangu, Water7 sebagai berikut :

- EniesLobby

Pada praktikum kali ini EniesLobby berperan menjadi DNS Server maka akan dilakukan instalasi bind9 dengan command sebagai berikut :

```
apt-get update
apt-get install bind9 -y
```

- Jipangu

Pada praktikum kali ini Jipangu berperan menjadi DHCP Server, maka akan dilakukan instalasi isc dhcp server dengan command sebagai berikut :

```
apt-get update
apt-get install isc-dhcp-server -y
```

- Water7

Pada praktikum kali ini Water7 berperan menjadi Proxy Server, maka akan dilakukan instalasi squid dengan command sebagai berikut :

```
apt-get update
apt-get install squid -y
```

# 2. Foosha sebagai DHCP Relay

Pada praktikum kali ini Foosha berperan menjadi DHCP Relay, maka akan dilakukan instalasi isc dhcp relay dengan command sebagai berikut :

```
apt-get update
apt-get install isc-dhcp-relay -y
```

Setelah dilakukan instalasi, akan dilakukan konfigurasi yang dapat diatur saat service isc-dhcp-relay start atau pun pada file `etc/default/isc-dhcp-relay` sebagai berikut :

![image](https://user-images.githubusercontent.com/70801807/141642727-b7eeab05-f82f-46e4-b100-9c7a8849f21e.png)


# Semua client yanga ada HARUS menggunakan konfigurasi IP dari DHCP Server.
Konfigurasi IP pada semua client dapat dilakukan dengan klik kanan pada masing-masing node client dan pilih `configure` lalu menuju ke `Edit network configuration`. Berikut adalah konfigurasi masing-masing client :

- Loguetown

![image](https://user-images.githubusercontent.com/70801807/141642807-30716e70-837f-46e7-8967-6bdc200e0ed3.png)

- Alabasta

![image](https://user-images.githubusercontent.com/70801807/141642834-6f17f322-a396-46e1-9f30-c9b42363b7b4.png)

- TottoLand

![image](https://user-images.githubusercontent.com/70801807/141642859-1b8438e4-0a69-45df-852c-8920994fd7b2.png)

- Skypie

![image](https://user-images.githubusercontent.com/70801807/141642881-337b6e01-6562-4457-864a-b3e3e9f9e55c.png)


### Syntax konfigurasi nomor 3, 4, 5, 6.

```
subnet 'NID' netmask 'Netmask' {
    range 'IP_Awal' 'IP_Akhir';
    option routers 'iP_Gateway';
    option broadcast-address 'IP_Broadcast';
    option domain-name-servers 'DNS_yang_diinginkan';
    default-lease-time 'Waktu';
    max-lease-time 'Waktu';
}
```


# 3.  Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.20 - [prefix IP].1.99 dan [prefix IP].1.150 - [prefix IP].1.169
Buka file dhcpd.conf yang terdapat di dalam folder `etc/dhcp` yang terdapat di Jipangu dan tambahkan syntax di bawah ini pada bagian paling bawah file tersebut :

```
subnet 10.30.1.0 netmask 255.255.255.0 {
    range 10.30.1.20 10.30.1.99;
    range 10.30.1.150 10.30.1.169;
    option routers 10.30.1.1;
    option broadcast-address 10.30.1.255;
}
```

### Testing :
Jika dilakukan pengetesan pada client Loguetown & Alabasta maka IP masing-masing client berada pada range sesuai settingan di atas:

Hasil command `ip a` pada masing-masing client :

- Loguetown

![image](https://user-images.githubusercontent.com/70801807/141643461-3028a873-0555-4b99-a39b-b9e3b3bc68c4.png)

- Alabasta

![image](https://user-images.githubusercontent.com/70801807/141643498-425aff29-cf48-4d24-904c-5a9fe5ab7b27.png)


# 4. Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.30 - [prefix IP].3.50 
Buka file dhcpd.conf yang terdapat di dalam folder `etc/dhcp` yang terdapat di Jipangu dan tambahkan syntax di bawah ini pada bagian paling bawah file tersebut di bawah konfigurasi switch 1:

```
subnet 10.30.3.0 netmask 255.255.255.0 {
    range 10.30.3.30 10.30.3.50;
    option routers 10.30.3.1;
    option broadcast-address 10.30.3.255;
}
```

### Testing :
Jika dilakukan pengetesan pada client TottoLand & Skypie maka IP masing-masing client berada pada range sesuai settingan di atas:

Hasil command `ip a` pada masing-masing client :

- TottoLand

![image](https://user-images.githubusercontent.com/70801807/141643515-22ec0308-f284-446d-8047-5bb961e381db.png)

- Skypie

![image](https://user-images.githubusercontent.com/70801807/141643651-f6fc99e8-cc04-4334-8927-81d7d4b81870.png)


# 5. Client mendapatkan DNS dari EniesLobby dan client dapat terhubung dengan internet melalui DNS tersebut

### a. Konfigurasi pada Jipangu
Buka file dhcpd.conf yang terdapat di dalam folder `etc/dhcp` yang terdapat di Jipangu dan tambahkan syntax di bawah ini pada konfigurasi switch 1 dan switch 3 setelah option broadcast-address:

```
    option domain-name-servers 10.30.2.2;
```

### b. Konfigurasi pada EniesLobby
- Edit file /etc/bind/named.conf.options pada server EniesLobby
- Uncomment pada bagian ini

```
    forwarders {
           192.168.122.1;
    };
```

- Comment pada bagian ini

```
// dnssec-validation auto;
```

- Dan tambahkan

```
allow-query{any;};
```

### Testing :
Akan dilakukan testing apakah setiap client dapat terhubung ke internet dengan melakukan ping terhadap google.com, berikut adalah hasilnya :

- Loguetown

![image](https://user-images.githubusercontent.com/70801807/141643757-2958f44d-35eb-4cf1-a18f-1d81a14d1ad7.png)

- Alabasta

![image](https://user-images.githubusercontent.com/70801807/141643739-b14181d4-6218-4b6c-8c44-a2409e70bb86.png)

- TottoLand

![image](https://user-images.githubusercontent.com/70801807/141643719-e0e118d7-5841-4af7-8c4b-39347ebd4e17.png)

- Skypie

![image](https://user-images.githubusercontent.com/70801807/141643703-7c2bf291-9c92-47e8-a6cc-864580663067.png)



# 6. Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 6 menit sedangkan pada client yang melalui Switch3 selama 12 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 120 menit.
- Buka file dhcpd.conf yang terdapat di dalam folder `etc/dhcp` yang terdapat di Jipangu
- Tambahkan syntax di bawah ini pada konfigurasi switch 1 setelah option domain-name-servers:

```
    default-lease-time 360;
    max-lease-time 7200;
```

- Tambahkan syntax di bawah ini pada konfigurasi switch 3 setelah option domain-name-servers:

```
    default-lease-time 720;
    max-lease-time 7200;
```

### Jika mengikuti langkah-langkah nomor 3-6 maka konfigurasi akhir untuk masing-masing switch pada file dhcpd.conf adalah sebagai berikut :

- Switch 1 :

```
subnet 10.30.1.0 netmask 255.255.255.0 {
    range 10.30.1.20 10.30.1.99;
    range 10.30.1.150 10.30.1.169;
    option routers 10.30.1.1;
    option broadcast-address 10.30.1.255;
    option domain-name-servers 10.30.2.2;
    default-lease-time 360;
    max-lease-time 7200;
}
```

- Switch 3 :

```
subnet 10.30.3.0 netmask 255.255.255.0 {
    range 10.30.3.30 10.30.3.50;
    option routers 10.30.3.1;
    option broadcast-address 10.30.3.255;
    option domain-name-servers 10.30.2.2;
    default-lease-time 720;
    max-lease-time 7200;
}
```

# 7. Luffy dan Zoro berencana menjadikan Skypie sebagai server untuk jual beli kapal yang dimilikinya dengan alamat IP yang tetap dengan IP [prefix IP].3.69
- Hasil command `ip a` pada Skypie sebelum konfigurasi :

![image](https://user-images.githubusercontent.com/70801807/141643651-f6fc99e8-cc04-4334-8927-81d7d4b81870.png)

- Buka file dhcpd.conf yang terdapat di dalam folder `etc/dhcp` yang terdapat di Jipangu
- Tambahkan syntax di bawah ini pada bagian paling bawah di file tersebut :

```
host Skypie{
        hardware ethernet b6:d1:f7:f9:74:e2;
        fixed-address 10.30.3.69;
}
```

- Edit network configuration Skypie menjadi berikut ini :

![image](https://user-images.githubusercontent.com/70801807/141643942-0afee065-f37e-4b30-be75-4ee41566887d.png)

- Hasil command `ip a` setelah konfigurasi:

![image](https://user-images.githubusercontent.com/70801807/141643958-5923fea1-3642-4535-9c0e-efcf9a59ac45.png)


# 8. Loguetown digunakan sebagai client Proxy agar transaksi jual beli dapat terjamin keamanannya, juga untuk mencegah kebocoran data transaksi.Pada Loguetown, proxy harus bisa diakses dengan nama jualbelikapal.yyy.com dengan port yang digunakan adalah 5000

Untuk mengakses jualbelikapal.e02.com, harus menyetting domain dulu pada EniesLobby sebagai DNS server.

Pertama, buka folder `/etc/bind/named.conf.local`. Lalu tambahkan configurasi di bawah ini.

```
zone "e02.com" {
    type master;
    file "/etc/bind/e02/e02.com";
};
```

Dengan begitu, konfigurasi domain akan berada pada folder `/etc/bind/e02/`.

Lalu buka file `/etc/bind/e02/e02.com` dan edit menjadi seperti di bawah ini.

Edit **SOA** menjadi `e02.com.`

```
...
@       IN      SOA     e02.com. root.e02.com.
...
```

Lalu ubah konfigurasi menjadi domain `e02.com.` dan tambahkan subdoamain `jualbelikapal` dengan IP mengarah ke `Jipangu`.

```
...
@               IN      NS      e02.com.
@               IN      A       10.30.2.2
jualbelikapal   IN      A       10.30.2.3
...
```

Dengan hasil akhir akan menjadi seperti ini

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     e02.com. root.e02.com. (
                     2021110805         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@               IN      NS      e02.com.
@               IN      A       10.30.2.2
jualbelikapal   IN      A       10.30.2.3
super.franky    IN      A       10.30.3.69

```

Setelah konfigurasi domain, selanjutnya adalah konfigurasi proxy. Buat file `squid.conf` pada folder `/etc/squid`, lalu edit file `/etc/squid/squid.conf` dan tambahkan port `5000` serta hostname yang digunakan yaitu `jualbelikapal.e02.com`

```
http_port 5000
visible_hostname jualbelikapal.e02.com
http_access allow all

```

Restart squid dengan syntax `service squid restart`.
Pada client, jalankan proxy dengan syntax `export http_proxy=http://jualbelikapal.e02.com:5000`.

![image](./images/no_8.1.png)

Setelah itu, ping `jualbelikapal.e02.com`

![image](./images/no_8.2.png)

# 9. Agar transaksi jual beli lebih aman dan pengguna website ada dua orang, proxy dipasang autentikasi user proxy dengan enkripsi MD5 dengan dua username, yaitu luffybelikapalyyy dengan password luffy_yyy dan zorobelikapalyyy dengan password zoro_yyy

Buat file `passwd` pada folder `/etc/squid` di `water7`. Lalu jalankan syntax

<ul>
    <li>
        htpasswd -m /etc/squid/passwd luffybelikapale02 dengan password luffy_e02
    </li>
    <li>
        htpasswd -m /etc/squid/passwd zorobelikapale02 dengan password zoro_e02
    </li>
</ul>

Lalu jika di-*cat* file `/etc/squid/passwd`, akan menghasilkan

![image](./images/no_9.1.png)

dan tambahkan syntax di bawah ini pada file `/etc/squid/squid.conf`

```
...
auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
auth_param basic children 5
auth_param basic realm Proxy
auth_param basic credentialsttl 2 hours
auth_param basic casesensitive on
acl USERS proxy_auth REQUIRED
...
http_access deny all
```

Sehingga akan terlihat seperti berikut
```
include /etc/squid/acl.conf
include /etc/squid/acl-bandwith.conf

http_port 5000
visible_hostname jualbelikapal.e02.com

auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
auth_param basic children 5
auth_param basic realm Proxy
auth_param basic credentialsttl 2 hours
auth_param basic casesensitive on

...

...

http_access deny all
```
Restart squid dengan syntax `service squid restart`. Lakukan `lynx its.ac.id`, maka akan diminta melakukan login.

![image](./images/no_9.2.png)

![image](./images/no_9.3.png)

![image](./images/no_9.4.png)

# 10.Transaksi jual beli tidak dilakukan setiap hari, oleh karena itu akses internet dibatasi hanya dapat diakses setiap hari Senin-Kamis pukul 07.00-11.00 dan setiap hari Selasa-Jum’at pukul 17.00-03.00 keesokan harinya (sampai Sabtu pukul 03.00)

Buat file `acl.conf` di folder `/etc/squid` di `water7`, edit file `/etc/squid/acl.conf` dan tambahkan syntax berikut.

```
acl AVAILABLE_WORKING time MTWH 07:00-11:00
acl AVAILABLE_WORKING1 time TWHF 17:00-24:00
acl AVAILABLE_WORKING2 time WHFA 00:00-03:00
```

Pada syntax tersebut, kita melakukan *assign* waktu untuk bisa mengakses web ke dalam variable **AVAILABLE WORKING**, **AVAILABLE WORKING1**, dan **AVAILABLE WORKING2**.

Selanjutnya adalah menambahkan konfigurasi file tersebut ke dalam file `squid.conf` dengan syntax `include /etc/squid/acl.conf` dan tambahkan syntax dibawah ini

```
include /etc/squid/acl.conf
...

acl USERS proxy_auth REQUIRED
http_access allow AVAILABLE_WORKING USERS
http_access allow AVAILABLE_WORKING1 USERS
http_access allow AVAILABLE_WORKING2 USERS
....

```

Sehingga akan terlihat seperti berikut

```
include /etc/squid/acl.conf
include /etc/squid/acl-bandwith.conf

http_port 5000
visible_hostname jualbelikapal.e02.com

auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
auth_param basic children 5
auth_param basic realm Proxy
auth_param basic credentialsttl 2 hours
auth_param basic casesensitive on
acl USERS proxy_auth REQUIRED
http_access allow AVAILABLE_WORKING USERS
http_access allow AVAILABLE_WORKING1 USERS
http_access allow AVAILABLE_WORKING2 USERS

...

http_access deny all
```

Setelah itu akses web dengan waktu yang tidak dibolehkan

![image](./images/no_10.1.png)

![image](./images/no_10.2.png)

# 11. Agar transaksi bisa lebih fokus berjalan, maka dilakukan redirect website agar mudah mengingat website transaksi jual beli kapal. Setiap mengakses google.com, akan diredirect menuju super.franky.yyy.com dengan website yang sama pada soal shift modul 2. Web server super.franky.yyy.com berada pada node Skypie

Ketika client mengakses google.com, client akan di-redirect menuju ke super.franky.e02.com. Strategi yang tim E02 lakukan untuk membuat hal ini terwujud adalah dengan memanfaatkan proxy server (Water 7). Pada server proxy, di konfigurasi server proxy (/etc/squid/squid.conf), ditambahkan kode sebagai berikut.

```bash
acl BLACKLIST dstdomain google.com
deny_info http://super.franky.e02.com/ BLACKLIST
http_reply_access deny BLACKLIST
```

![image](https://user-images.githubusercontent.com/8071604/141642411-283f5d09-ecf1-47b0-8e0a-d49397003a22.png)

Gambar 11.1 Tampilan `vim /etc/squid/squid.conf`

Setelah kode tersebut ditambahkan pada konfigurasi proxy server, proxy server di-restart.

![image](https://user-images.githubusercontent.com/8071604/141642522-e4808dd1-45a4-430a-83d3-825a89bee8aa.png)

Gambar 11.2 Tampilan `service squid restart`

Kemudian, pasang proxy pada client Loguetown dengan menggunakan command berikut ini.

```bash
export http_proxy=http://jualbelikapal.e02.com:5000
```

Setelah proxy dipasang, uji coba dilakukan dengan menggunakan aplikasi lynx pada client Loguetown.

```bash
lynx google.com
```

![image](https://user-images.githubusercontent.com/8071604/141642628-94b8ffd8-9fe0-47db-a8da-3ff2de98d983.png)

Gambar 11.3 Tampilan `lynx google.com` di Loguetown

Pada Gambar 11.3, ditunjukkan bahwa browser lynx di-redirect menuju halaman http://super.franky.e02.com. 

# 12 & 13. Saatnya berlayar! Luffy dan Zoro akhirnya memutuskan untuk berlayar untuk mencari harta karun di super.franky.e02.com. Tugas pencarian dibagi menjadi dua misi, Luffy bertugas untuk mendapatkan gambar (.png, .jpg), sedangkan Zoro mendapatkan sisanya. Karena Luffy orangnya sangat teliti untuk mencari harta karun, ketika ia berhasil mendapatkan gambar, ia mendapatkan gambar dan melihatnya dengan kecepatan 10 kbps. Sedangkan, Zoro yang sangat bersemangat untuk mencari harta karun, sehingga kecepatan kapal Zoro tidak dibatasi ketika sudah mendapatkan harta yang diinginkannya

Luffy dapat mengakses proxy dengan username:password adalah luffybelikapale02:luffy_e02. Sedangkan, Zoro dapat mengakses proxy dengan username:password adalah zorobelikapale02:zoro_e02.
Ketika Luffy mendownload file berektensi .jpg atau .png, kecepatan downloadnya harus dibatasi menjadi 10 kbps. Di lain sisi, ketika Zoro mendownload file apapun, kecepatan downloadnya tidak dibatasi.

Pertama-tama, konversi 10 kbps (kilobit per second) menjadi unit Bps (byte per second). 1 bit adalah 0.125 byte. Maka, 10 kbps sama dengan 1250 Bps.

Kedua, buka file /etc/squid/acl-bandwith.conf dan tambahkan kode untuk membatasi bandwith Luffy ketika mendownload file berektensi .jpg atau .png.

```bash
acl download url_regex -i \.jpg$ \.png$
auth_param basic program /user/lib/squid/basic_ncsa_auth /etc/squid/passwd

acl luffy proxy_auth luffybelikapale02
acl zoro proxy_auth zorobelikapale02

delay_pools 2
delay_class 1 1
delay_parameters 1 1250/1250
delay_access 1 allow luffy
delay_access 1 deny zoro
delay_access 1 allow download
delay_access 1 deny all
```

Penjelasan kode:
1. `acl download url_regex -i \.jpg$ \.png$`: mendefinisikan `download` adalah URL dengan akhiran .jpg atau .png.
2. `auth_param basic program /user/lib/squid/basic_ncsa_auth /etc/squid/passwd`: mendefinisikan lokasi username dan password (/etc/squid/passwd).
3. `acl luffy proxy_auth luffybelikapale02`: mendefinisikan `luffy` adalah akses proxy yang menggunakan username `luffybelikapale02`.
4. `acl zoro proxy_auth zorobelikapale02`: mendefinisikan `zoro` adalah akses proxy yang menggunakan username `zorobelikapale02`.
5. `delay_pools 2`: mendefinisikan banyak pool adalah 2.
6. `delay_class 1 1`: mendefinisikan class pembagian bandwith dari setiap pool 1. 1 artinya semua bandwidth yang ada akan dibagi sama rata untuk semua user squid.
7. `delay_parameters 1 1250/1250`: mendefinisikan aturan pada pool 1 yaitu restore = 1250 dan max = 1250. Artinya, jika file yang lewat berukuran lebih besar dari 1250 byte, maka bandwith-nya akan dibatasi sebesar 1250 byte perdetik.
8. `delay_access 1 allow luffy`: mendefinisikan bahwa pool 1 dapat digunakan oleh user luffy.
9. `delay_access 1 deny zoro`: mendefinisikan bahwa pool 2 tidak dapat digunakan oleh user zoro karena zoro tidak boleh mendapatkan pembatasan bandwith.
10. `delay_access 1 allow download`: mendefinisikan bahwa aturan `download` yang telah didefinisikan pada baris satu, boleh melalui pool 1.
11. `delay_access 1 deny all`: mendefinisikan bahwa selain aturan `download`, tidak boleh melalui pool 1.

![image](https://user-images.githubusercontent.com/8071604/141642953-76ac8681-5faf-47fc-9040-103143271623.png)

Gambar 12.1 Tampilan `vim /etc/squid/acl-bandwith.conf`

Setelah file acl-bandwith.conf dibuat, maka di dalam file /etc/squid/squid.conf harus ditambahkan / di-include file acl-bandwith.conf.

```bash
include /etc/squid/acl-bandwith.conf
```

![image](https://user-images.githubusercontent.com/8071604/141643707-09057ee3-d0fc-4374-b7a0-0a98aa6c829b.png)

Gambar 12.2 Tampilan `vim /etc/squid/squid.conf`

Setelah itu, proxy server di-restart dengan menggunakan perintah `service squid restart`.

![image](https://user-images.githubusercontent.com/8071604/141643782-bb263fc4-f1cb-421e-9278-615890bf5e44.png)

Gambar 12.3 Tampilan `service squid restart`

Kemudian uji coba dilakukan pada client Alabasta. Pertama, install `wget` (`apt-get install wget`) dan buka file `/etc/wgetrc` pada client Alabasta.

![image](https://user-images.githubusercontent.com/8071604/141643861-571f90cb-381b-4afc-a9f3-80d8c346588e.png)

Gambar 12.4 Tampilan `apt-get install wget`

Di dalam file `/etc/wgetrc`, uncomment bagian `use_proxy = on` dan set value dari `http_proxy` menjadi `http://[username]:[password]@[host]:[port]`. 
Pada kasus ini, username yang akan diuji adalah luffybelikapale02 dengan password luffy_e02 di host jualbelikapal.e02.com di port 5000.

```bash
http_proxy = http://luffybelikapale02:luffy_e02@jualbelikapal.e02.com:5000
use_proxy = on
```

![image](https://user-images.githubusercontent.com/8071604/141643956-461076d0-0864-4264-a1e9-bd51306df403.png)

Gambar 12.5 Tampilan `vim /etc/wgetrc`

Setelah itu, simpan `/etc/wgetrc` dan `wget http://super.franky.e02.com/public/images/car.jpg` dilakukan untuk menguji coba download file car.jpg yang ada di web server Skypie melalui proxy dengan user luffy.

![image](https://user-images.githubusercontent.com/8071604/141644002-45b51bdd-bdd4-4ee4-91a2-b1b2e3438351.png)

Gambar 12.6 Tampilan wget dengan kecepatan < 10 kbps dengan user luffy

Berdasarkan hasil uji coba yang ditunjukkan pada Gambar 12.6, user luffy mendapatkan limitasi bandwith sehingga bandwith-nya tidak dapat melebihi 10kbps. Di lain sisi, uji coba dilakukan untuk user zoro dengan mengubah value `http_proxy` pada `/etc/wgetrc` menjadi `http://zorobelikapale02:zoro_e02@jualbelikapal.e02.com:5000`.

![image](https://user-images.githubusercontent.com/8071604/141644069-f33f39ec-4c46-4c0d-9e6e-4002ab25f2ac.png)

Gambar 12.7 Tampilan wget tanpa batasan kecepatan dengan user zoro

Berdasarkan hasil uji coba, wget dengan user zoro jauh lebih cepat daripada luffy. Tidak perlu menunggu, file yang didownload zoro langsung dapat diterima oleh Alabasta.
Dengan demikian, kecepatan download luffy dibatasi maksimal 10kbps ketika mengakses file car.jpg dan kecepatan zoro tidak dibatasi ketika mengakses file car.jpg

# Kesulitan yang dialami selama mengerjakan soal shift modul 3
1. Keterbatasan dokumentasi pada aplikasi squid. Di internet, dokumentasi squid masih minim sehingga butuh waktu lumayan lama untuk mempelajarinya.
