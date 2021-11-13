# Jarkom-Modul-3-E02-2021

# 1. Mengatur EniesLobby sebagai DNS Server, Jipangu sebagai DHCP Server, Water7 sebagai Proxy Server.

Berdasarkan soal di atas maka akan dilakukan beberapa konfigurasi pada node EniesLobby, Jipangu, Water7 sebagai berikut :

-   EniesLobby

-   Jipangu

-   Water7

# 2. Foosha sebagai DHCP Relay

Berikut ini adalah instalasi yang dilakukann pada node Foosha untuk menjadikannya DHCP Relay :

# Semua client yanga ada HARUS menggunakan konfigurasi IP dari DHCP Server.

# Syntax konfigurasi nomor 3, 4, 5, 6.

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

# 3. Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.20 - [prefix IP].1.99 dan [prefix IP].1.150 - [prefix IP].1.169

# 4. Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.30 - [prefix IP].3.50

# 5. Client mendapatkan DNS dari EniesLobby dan client dapat terhubung dengan internet melalui DNS tersebut

# 6. Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 6 menit sedangkan pada client yang melalui Switch3 selama 12 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 120 menit.

# 7. Luffy dan Zoro berencana menjadikan Skypie sebagai server untuk jual beli kapal yang dimilikinya dengan alamat IP yang tetap dengan IP [prefix IP].3.69

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
