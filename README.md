# Jarkom-Modul-5-E05


Kelompok E05

Anggota:  
* Maula Izza Azizi - 5025201104
* Selfira Ayu Sheehan - 5025201174
* Brian Akbar Wicaksana - 5025201207

## Soal Modul 5
Setelah kalian mempelajari semua modul yang telah diberikan, Loid ingin meminta bantuan untuk terakhir kalinya kepada kalian. Dan kalian dengan senang hati mau membantu Loid.

1. Tugas pertama kalian yaitu membuat topologi jaringan sesuai dengan rancangan yang diberikan Loid dibawah ini:
<img width="809" alt="Screen Shot 2022-12-10 at 02 17 09" src="https://user-images.githubusercontent.com/72302421/206780313-b21e54ae-541f-4c3e-9363-d02cc6d5cf2f.png">

```
Keterangan :	
- Eden adalah DNS Server
- WISE adalah DHCP Server
- Garden dan SSS adalah Web Server
- Jumlah Host pada Forger adalah 62 host
- Jumlah Host pada Desmond adalah 700 host
- Jumlah Host pada Blackbell adalah 255 host
- Jumlah Host pada Briar adalah 200 host
```

2 Untuk menjaga perdamaian dunia, Loid ingin meminta kalian untuk membuat topologi tersebut menggunakan teknik CIDR atau VLSM setelah melakukan subnetting.

3 Anya, putri pertama Loid, juga berpesan kepada anda agar melakukan Routing agar setiap perangkat pada jaringan tersebut dapat terhubung.

4 Tugas berikutnya adalah memberikan ip pada subnet Forger, Desmond, Blackbell, dan Briar secara dinamis menggunakan bantuan DHCP server. Kemudian kalian ingat bahwa kalian harus setting DHCP Relay pada router yang menghubungkannya.

### Perhitungan CIDR
<img width="677" alt="Screen Shot 2022-12-10 at 02 28 21" src="https://user-images.githubusercontent.com/72302421/206781988-43313ed1-e02b-4c8e-b181-5089976c1574.png">

<img width="467" alt="Screen Shot 2022-12-10 at 02 30 05" src="https://user-images.githubusercontent.com/72302421/206782299-89b95f96-0ee1-4bbc-9058-cdfc70ab9964.png">

<img width="421" alt="Screen Shot 2022-12-10 at 02 30 51" src="https://user-images.githubusercontent.com/72302421/206782387-d0c253ae-afce-40e9-9b21-be5afde5bc43.png">

<img width="439" alt="Screen Shot 2022-12-10 at 02 31 35" src="https://user-images.githubusercontent.com/72302421/206782505-78f1fac6-b06d-44d2-8632-45aafa7f7bdb.png">

<img width="729" alt="Screen Shot 2022-12-10 at 02 33 05" src="https://user-images.githubusercontent.com/72302421/206782730-620ce92b-526e-4e1a-b80c-2d269c026fcd.png">

### Konfigurasi Network setiap node
`Eden`
```
auto eth0
iface eth0 inet static
	address 10.24.0.130
	netmask 255.255.255.248
        gateway 10.24.0.129
```

`WISE`
```
auto eth0
iface eth0 inet static
	address 10.24.0.131
	netmask 255.255.255.248
        gateway 10.24.0.129
```

`Garden`
```
auto eth0
iface eth0 inet static
	address 10.24.17.2
	netmask 255.255.255.248
        gateway 10.24.17.1
```

`SSS`
```
auto eth0
iface eth0 inet static
	address 10.24.17.3
	netmask 255.255.255.248
        gateway 10.24.17.1
```

`Forger(62Host)`
```
auto eth0
iface eth0 inet dhcp
```

`Desmond(700Host)`
```
auto eth0
iface eth0 inet static
	address 10.24.1.2
	netmask 255.255.252.0
        gateway 10.24.1.1
```

`Blackbell(255Host)`
```
auto eth0
iface eth0 inet dhcp
```

`Briar(200Host)`
```
auto eth0
iface eth0 inet dhcp
```

### Routing
* Strix
```
route add -net 10.24.0.0 netmask 255.255.248.0 gw 10.24.8.2
route add -net 10.24.16.0 netmask 255.255.252.0 gw 10.24.20.2
```

* Ostania
```
route add -net 0.0.0.0 netmask 0.0.0.0 gw 10.24.20.1
```

* Westalis
```
route add -net 0.0.0.0 netmask 0.0.0.0 gw 10.24.8.1
```
### DHCP
* WISE sebagai DHCP Server: 
	Pertama dilakukan instalasi sebagai berikut
	```
	apt-get update
	apt-get install isc-dhcp-server -y
	```  
	Setelah itu, pada ```isc-dhcp-server```, interface di-set menjadi eth0 dengan command
	```
	INTERFACES="eth0"
	```
	Lalu file ```dhcpd.conf``` diedit menjadi:
	```
	subnet 10.24.0.128 netmask 255.255.255.248 {
	}

	subnet 10.24.0.0 netmask 255.255.255.128 {
	    range 10.24.0.2 10.24.0.126;
	    option routers 10.24.0.1;
	    option broadcast-address 10.24.0.127;
	    option domain-name-servers 10.24.0.130;
	    default-lease-time 600;
	    max-lease-time 7200;
	}

	subnet 10.24.1.0 netmask 255.255.252.0 {
	    range 10.24.1.2 10.24.4.254;
	    option routers 10.24.1.1;
	    option broadcast-address 10.24.4.255;
	    option domain-name-servers 10.24.0.130;
	    default-lease-time 600;
	    max-lease-time 7200;
	}
	
	subnet 10.24.16.0 netmask 255.255.255.0 {
	    range 10.24.16.2 10.24.16.254;
	    option routers 10.24.16.1;
	    option broadcast-address 10.24.16.255;
	    option domain-name-servers 10.24.0.130;
	    default-lease-time 600;
	    max-lease-time 7200;
	}

	subnet 10.24.18.0 netmask 255.255.254.0 {
	    range 10.24.18.2 10.24.19.254;
	    option routers 10.24.18.1;
	    option broadcast-address 10.24.19.255;
	    option domain-name-servers 10.24.0.130;
	    default-lease-time 600;
	    max-lease-time 7200;
	}
	```
	Terakhir lakukan restart dengan ```service isc-dhcp-server start```.

* Westalis, Strix, Ostania sebagai DHCP Relay:
	Pada masing-masing router (Westalis, Strix, dan Ostania), jalankan:
	* Strix
	```
	apt-get update
	apt-get install dhcp-helper -y

	dhcp-helper -s 10.24.8.2
	```
	* Westalis
	```
	apt-get update
	apt-get install dhcp-helper -y

	dhcp-helper -s 10.24.0.131
	```
	* Ostania
	```
	apt-get update
	apt-get install dhcp-helper -y

	dhcp-helper -s 10.24.20.1
	```
	Lalu network tiap client dikonfigurasikan sebagai berikut
	```
	auto eth0
	iface eth0 inet dhcp
	```
