# Jarkom-Modul-3-E02-2021

# 1. Mengatur EniesLobby sebagai DNS Server, Jipangu sebagai DHCP Server, Water7 sebagai Proxy Server.

Berdasarkan soal di atas maka akan dilakukan beberapa konfigurasi pada node EniesLobby, Jipangu, Water7 sebagai berikut :

- EniesLobby


- Jipangu


- Water7


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



# 3.  Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.20 - [prefix IP].1.99 dan [prefix IP].1.150 - [prefix IP].1.169

# 4. Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.30 - [prefix IP].3.50 

# 5. Client mendapatkan DNS dari EniesLobby dan client dapat terhubung dengan internet melalui DNS tersebut

# 6. Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 6 menit sedangkan pada client yang melalui Switch3 selama 12 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 120 menit.

# 7. Luffy dan Zoro berencana menjadikan Skypie sebagai server untuk jual beli kapal yang dimilikinya dengan alamat IP yang tetap dengan IP [prefix IP].3.69
