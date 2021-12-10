# Jarkom-Modul-5-T1-2021

## Anggota Kelompok

- Prima Secondary Ramadhan  05311940000001
- Asiyah Hanifah            05311940000002
- Widya Inayatul            05311940000010
---
## Soal A
membuat topologi sesuai yang diminta pada soal seperti gambar berikut ini :
![topologimodul5](https://user-images.githubusercontent.com/73151978/145414188-0c5a5a99-96ee-4b64-b8f0-e01874ffb52c.png)

```
Keterangan :  Doriki adalah DNS Server
              Jipangu adalah DHCP Server
              Maingate dan Jorge adalah Web Server
              Jumlah Host pada Blueno adalah 100 host
              Jumlah Host pada Cipher adalah 700 host
              Jumlah Host pada Elena adalah 300 host
              Jumlah Host pada Fukurou adalah 200 host
```

## Soal B
Luffy ingin meminta kalian untuk membuat topologi tersebut menggunakan teknik CIDR atau VLSM. setelah melakukan subnetting,
Disini kami menggunakan teknik VLSM. Dari hasil pembagian subnet, kami mendapatkan sejumlah 8 subnet yang terdiri atas 6 subnet untuk router-router dan router-client (A1, A2, A3, A4,A5,A6) dan 2 subnet untuk router-server (A7 dan A8) Seperti gambar berikut ini. 

![Frame 1 (1)](https://user-images.githubusercontent.com/73151978/145415968-7d516f84-c682-4136-844f-20a5b2beb837.png)

### Subnetting
menghitung pembagian IP berdasarkan NID dan netmask menggunakan pohon seperti gambar di bawah. Kemudian dilakukan subnetting dengan menggunakan pohon untuk pembagian IP sesuai dengan kebutuhan masing-masing subnet yang ada.

![TreeVLSM Jarkom - VLSM MODUL5](https://user-images.githubusercontent.com/73151978/145416992-f3f69043-b343-4150-a41b-4bcd9efda688.jpg)

Dari pohon tersebut akan mendapat pembagian IP dengan melakukan perhitungan. Hasil perhitungan Network ID, Netmask, Broadcast Address dari subnet yang telah ditentukan netmasknya. Contoh perhitungan: pada subnet A1 yang memiliki Network ID yaitu 10.42.4.0 dengan Netmasknya 255.255.248.0 yang didapatkan berdasarkan pohonnya sesuai tabel di atas. Kemudian untuk menentukan broadcast address kami lakukan perhitungan sebagai berikut.

```
IP			        : 10.42.4.0
Bit			        : 00001010.00101010.00000 100.00000000
Netmask		      : 255.255.248.0
Bit			        : 11111111.11111111.11111 000.00000000
Invert Netmask	: 00000000.00000000.00000 111.11111111
Kemudian melakukan operasi OR pada IP dan Invert Netmasknya.
IP			        : 00001010.00101010.00000 100.00000000
Netmask		      : 00000000.00000000.00000 111.11111111
------------------------------------------------------------------------------------------------ Operasi OR
Bit Broadcast	  : 00001010.00101010.00000 111.11111111
Broadcast		    : 10.42.7.255 
```
Sehingga didapatkan Broadcast Address dari subnet A1 dengan NID 10.42.4.0 adalah 10.42.7.255. Dari perhitungan tersebut, kami dapatkan hasil perhitungan Network ID, Netmask, Broadcast Address dari semua subnet pada tabel berikut.

![tabelmodul5](https://user-images.githubusercontent.com/73151978/145419297-ceb7b62b-c5c6-40c9-b707-f418f00c78b2.png)

## Soal C
melakukan Routing agar setiap perangkat pada jaringan tersebut dapat terhubung.

1. Foosha
```
# masquerade dengan netmask terbesar /21
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.42.0.0/21

#Kiri Foosha

# ke a2 Blueno
route add -net 10.42.0.128 netmask 255.255.255.128 gw 10.42.0.2

# ke a1 client Cipher
route add -net 10.42.4.0 netmask 255.255.252.0 gw 10.42.0.2

# ke a8 client Doriki & Jipangu
route add -net 10.42.0.16 netmask 255.255.255.248 gw 10.42.0.2


#Kanan Foosha

# ke a5 Elena
route add -net 10.42.2.0 netmask 255.255.254.0 gw 10.42.0.6

# ke a6 client Fukurou
route add -net 10.42.1.0 netmask 255.255.255.0 gw 10.42.0.6
```
2. Water 7
pertama menjalankan command ```echo nameserver 192.168.122.1 > /etc/resolv.conf``` agar dapat melakukan ping 

```
#kanan Water 7

#ke a7 Jorge & Maingate
route add -net 10.42.0.8 netmask 255.255.255.248 gw 10.42.0.1

# ke a5 Elena
route add -net 10.42.2.0 netmask 255.255.254.0 gw 10.42.0.1

# ke a6 client Fukurou
route add -net 10.42.1.0 netmask 255.255.255.0 gw 10.42.0.1

# ke a4 Guanhao
route add -net 10.42.0.4 netmask 255.255.255.252 gw 10.42.0.1
```
Selanjutnya dilakukan testing pada ELENA dengan cara ```ping 10.42.2.2```

3. Guanhao

```
#ke kiri 

#ke a2 water 7
route add -net 10.42.0.128 netmask 255.255.255.128 gw 10.42.0.5

#ke a2 Blueno
route add -net 10.42.0.128 netmask 255.255.255.128 gw 10.42.0.5

#ke a1 Cipher
route add -net 10.42.4.0 netmask 255.255.252.0 gw 10.42.0.5

#ke a7 Jipangu & Doriki
route add -net 10.42.0.16 netmask 255.255.255.248 gw 10.42.0.5

```
## Soal D
Tugas berikutnya adalah memberikan ip pada subnet Blueno, Cipher, Fukurou, dan Elena secara dinamis menggunakan bantuan DHCP server. Kemudian kalian ingat bahwa kalian harus setting DHCP Relay pada router yang menghubungkannya


### Jipangu
Pertama kami menghubungkan ke internet dan melakukan install DHCP Server menggunakan command pada JIPANGU adalah 
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get install isc-dhcp-server
```
kemudian install nano dengan perintah ```apt-get install nano```
dan edit konfigurasi seperti berikut.
```
echo 
‘
INTERFACES="eth0"
’> /etc/default/isc-dhcp-server
```

### Foosha
Selanjutnya konfigurasi DHCP relay pada FOOSHA dengan menginstall DHCP Relay menggunakan
command ```apt-get install isc-dhcp-relay -y```
kemudian edit konfigurasi dengan setting sysctl untuk IP forwarding dan edit konfigurasi isc-dhcp-relay dengan server jipangu lalu dilakukan restart dhcp relay seperti berikut ini.
```
echo
‘
net.ipv4.ip_forward=1
‘ > /etc/sysctl.conf
sysctl -p 
echo '
SERVERS="10.42.0.18"
INTERFACES="eth1 eth2"
OPTIONS=""
' > /etc/default/isc-dhcp-relay
service isc-dhcp-relay restart
```
### Water7
sama seperti sebelumnya konfigurasi DHCP relay pada WATER7 dengan menginstall DHCP Relay menggunakan
command ```apt-get install isc-dhcp-relay -y```
kemudian edit konfigurasi dengan setting sysctl untuk IP forwarding dan edit konfigurasi isc-dhcp-relay dengan server jipangu lalu dilakukan restart dhcp relay seperti berikut ini.
```
apt-get install isc-dhcp-relay -y
echo
‘
net.ipv4.ip_forward=1
‘ > /etc/sysctl.conf
sysctl -p
echo '
SERVERS="10.42.0.18"
INTERFACES="eth0 eth1 eth2 eth3"
OPTIONS=""
' > /etc/default/isc-dhcp-relay
service isc-dhcp-relay restart
```
### Guanhao
sama seperti sebelumnya konfigurasi DHCP relay pada Guanhao dengan menginstall DHCP Relay menggunakan
command ```apt-get install isc-dhcp-relay -y```
namun tidak setting sysctl untuk IP forwarding dan edit konfigurasi isc-dhcp-relay dengan server jipangu lalu dilakukan restart dhcp relay seperti berikut ini.
```
apt-get install isc-dhcp-relay -y
echo '
SERVERS="10.42.0.18"
INTERFACES="eth0 eth1 eth2 eth3"
OPTIONS=""
' > /etc/default/isc-dhcp-relay
service isc-dhcp-relay restart
```
Agar DHCP Server JIPANGU dapat berjalan dengan lancar, maka kami melakukan deklarasi subnet yang terkoneksi pada JIPANGU yaitu subnet A8, A1, A6, dan A5 pada file ```etc/dhcp/dhcpd.conf```

### Jipangu
Selanjutnya dilakukan deklarasi untuk subnet A8 ini hanya harus dideklarasikan, tetapi tidak harus memilikisettingan DHCP
```
echo
'
#A8
subnet 10.42.0.16 netmask 255.255.255.248 {
}
 
#A1 (CIPHER)
subnet 10.42.4.0 netmask 255.255.252.0 {
range 10.42.4.2 10.42.4.702;
option routers 10.42.4.1;
option broadcast-address 10.42.7.255;
option domain-name-servers 10.42.0.19 ,202.46.129.2;
default-lease-time 600;
max-lease-time 7200;
}
 
#A2 (BLUENO)
subnet 10.42.0.128 netmask 255.255.255.128 {
range 10.42.0.130 10.42.1.230;
option routers 10.42.0.129;
option broadcast-address 10.42.0.255;
option domain-name-servers 10.42.0.19 ,202.46.129.2;
default-lease-time 600;
max-lease-time 7200;
}
 
#A6 (FUKUROU)
subnet 10.42.1.0 netmask 255.255.255.0 {
range 10.42.1.2 10.42.1.202;
option routers 10.42.1.1;
option broadcast-address 10.42.1.255;
option domain-name-servers 10.42.0.19 ,202.46.129.2;
default-lease-time 600;
max-lease-time 7200;
}
 
#A5 (ELENA)
subnet 10.42.2.0 netmask 255.255.254.0 {
range 10.42.2.2 10.42.2.302;
option routers 10.42.2.1;
option broadcast-address 10.42.3.255;
option domain-name-servers 10.42.0.19 ,202.46.129.2;
default-lease-time 600;
max-lease-time 7200;
}
' > /etc/dhcp/dhcpd.conf
```
## NO 1
Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Foosha menggunakan iptables, tetapi Luffy tidak ingin menggunakan MASQUERADE.

Pada Foosha dilakukan konfigurasi seperti berikut ini.
### Foosha

```
echo '
auto eth0
iface eth0 inet static
    address 192.168.122.2
    netmask 255.255.255.0
    broadcast 192.168.122.1
auto eth1
iface eth1 inet static
    address 10.42.0.5
    netmask 255.255.255.252
    broadcast 10.42.0.7
 
auto eth2
iface eth2 inet static
    address 10.42.0.1
    netmask 255.255.255.252
    broadcast 10.42.0.3
' > /etc/network/interfaces
```

kami menggunakan command -t nat NAT Table pada -A POSTROUTING POSTROUTING chain untuk -jSNAT mengubah source address yang awalnya berupa private IPv4 address yang memiliki 16-bit blok dari private IP addresses yaitu -s 10.42.0.0/21 menjadi --to-source 192.168.122.2 IP eth0 Foosha yaitu 192.168.122.2 karena Foosha adalah satu satunya router yang terhubung ke cloud melalui eth0. 

```
iptables -t nat -A POSTROUTING -s 10.42.0.0/21 -o eth0 -j SNAT --to-source 192.168.122.2
```

Di sini kami menggunakan -t nat NAT Table pada -A POSTROUTING POSTROUTING chain untuk -j SNAT mengubah source address yang awalnya berupa private IPv4 address yang memiliki 16-bit blok dari private IP addresses yaitu -s 192.168.0.0/16 10.42.0.0/21  menjadi --to-source 192.168.122.2 IP eth0 Foosha yaitu 192.168.122.2 karena Foosha adalah router yang terhubung ke cloud.

> Testing

Untuk testing, kami mencoba kirim ping keluar pada Foosha dan Jipangu
### Foosha
```
ping 8.8.8.8
```
### Jipangu
```
ping 8.8.8.8
```
## NO 2
Kalian diminta untuk mendrop semua akses HTTP dari luar Topologi kalian pada server yang memiliki ip DHCP dan DNS Server demi menjaga keamanan.

> Setting
### Foosha
```
iptables -A FORWARD -d 10.42.0.16/29 -i eth0 -p tcp -m tcp --dport 80 -j DROP
```

Kita menggunakan -A FORWARD FORWARD chain untuk menyaring paket dengan -p tcp -m tcp protokol TCP dari luar topologi menuju ke DHCP Server JIPANGU dan DNS Server DORIKI (yang berada di satu subnet yang sama yaitu -d 10.42.0.16/29), dimana akses SSH (yang memiliki --dport 80 port 80) yang masuk ke DHCP Server JIPANGU dan DNS Server DORIKI melalui -i eth0 interfaces eth0 dari DHCP Server JIPANGU dan DNS Server DORIKI agar -j DROP di DROP
> Testing
### Elena
```
nmap -p 80 10.42.2.2
```

![](https://media.discordapp.net/attachments/854638984004239380/918751878014173245/unknown.png?width=1436&height=314)

## NO 3
Karena kelompok kalian maksimal terdiri dari 3 orang. Luffy meminta kalian untuk membatasi DHCP dan DNS Server hanya boleh menerima maksimal 3 koneksi ICMP secara bersamaan menggunakan iptables, selebihnya didrop.

> Setting
### Jipangu
```
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```
### Doriki
```
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```

Kita menggunakan -A INPUT INPUT chain untuk menyaring paket dengan -p icmp agar protokol ICMP atau ping yang masuk agar dibatasi -m connlimit --connlimit-above 3 hanya sebatas maksimal 3 koneksi saja --connlimit-mask 0 darimana saja, sehingga lebih dari itu akan -j DROP di DROP

> Testing

### Elena
```
ping 10.42.0.18
```
![](https://media.discordapp.net/attachments/854638984004239380/918751980166467604/unknown.png)
### Fukurou
```
ping 10.42.0.18
```
![](https://media.discordapp.net/attachments/854638984004239380/918752056720916520/unknown.png)
### Blueno
```
ping 10.42.0.18
```
![](https://media.discordapp.net/attachments/854638984004239380/918752118188437534/unknown.png)
### Cipher
```
ping 10.42.0.18
```
![](https://media.discordapp.net/attachments/854638984004239380/918752168381677578/unknown.png)
## NO 4
Akses dari subnet Blueno dan Cipher hanya diperbolehkan pada  pukul 07.00 - 15.00 pada hari Senin sampai Kamis.

> Setting
### Doriki
```
# cipher
iptables -A INPUT -s 10.42.4.0/22 -m time --weekdays Fri,Sat,Sun -j REJECT
iptables -A INPUT -s 10.42.4.0/22 -m time --timestart 00:00 --timestop 06:59 --weekdays Mon,Tue,Wed,Thu -j REJECT
iptables -A INPUT -s 10.42.4.0/22 -m time --timestart 15:01 --timestop 23:59 --weekdays Mon,Tue,Wed,Thu -j REJECT

# blueno
iptables -A INPUT -s 10.42.0.128/25 -m time --weekdays Fri,Sat,Sun -j REJECT
iptables -A INPUT -s 10.42.0.128/25 -m time --timestart 00:00 --timestop 06:59 --weekdays Mon,Tue,Wed,Thu -j REJECT
iptables -A INPUT -s 10.42.0.128/25 -m time --timestart 15:01 --timestop 23:59 --weekdays Mon,Tue,Wed,Thu -j REJECT
```

- Di sini kami menggunakan -A INPUT INPUT chain untuk menyaring paket yang masuk dari -s 10.42.4.0/22 subnet CIPHER dan 10.42.0.128/25 subnet BLUENO
- Untuk -m time --weekdays Fri, Sat,Sun di jam berapapun pada hari Jumat, Sabtu dan Minggu agar -j REJECT ditolak.

- -m time --timestart 00:00 --timestop 06:59 di waktu jam 00:00 sampai dengan jam 06:59 --weekdays Mon,Tue,Wed,Thu pada hari Senin, Selasa, Rabu, Kamis agar -j REJECT ditolak

- -m time --timestart 15:01 --timestop 23:59 di waktu jam 15:01 sampai dengan jam 23:59 --weekdays Mon,Tue,Wed,Thu pada hari Senin, Selasa, Rabu, Kamis agar -j REJECT ditolak 

> Testing
### Cipher
```
ping 10.42.0.19
```
![](https://cdn.discordapp.com/attachments/854638984004239380/918754041402626058/unknown.png)
### Blueno
```
ping 10.42.0.19
```
![](https://media.discordapp.net/attachments/854638984004239380/918754090039795712/unknown.png?width=1436&height=337)
## NO 5
Akses dari subnet Elena dan Fukuro hanya diperbolehkan pada pukul 15.01 hingga pukul 06.59 setiap harinya.

> Setting
### Doriki
```
iptables -A INPUT -s 10.42.2.0/23 -m time --timestart 07:00 --timestop 15:00 -j REJECT

iptables -A INPUT -s 10.42.1.0/24 -m time --timestart 07:00 --timestop 15:00 -j REJECT
```
Di sini digunakan -A INPUT INPUT chain untuk menyaring paket yang masuk dari -s 10.42.2.0/23 subnet ELENA dan 10.42.1.0/24 subnet Fukurou -m time --timestart 07:00 --timestop 15:00 di waktu jam 07:00 sampai dengan jam 15:00  di hari apapun untuk batasan aksesnya -j REJECT ditolak jika diluar batas waktunya
> Testing
### Elena
```
ping 10.42.0.19
```
![](https://media.discordapp.net/attachments/854638984004239380/918754174643089518/unknown.png?width=1436&height=428)
### Fukuro
```
ping 10.42.0.19
```
![](https://media.discordapp.net/attachments/854638984004239380/918754218188374016/unknown.png?width=1436&height=430)
## NO 6
Karena kita memiliki 2 Web Server, Luffy ingin Guanhao disetting sehingga setiap request dari client yang mengakses DNS Server akan didistribusikan secara bergantian pada Jorge dan Maingate

```
iptables -A PREROUTING -t nat -p tcp -d 10.42.0.19 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 10.42.0.11:80

iptables -A PREROUTING -t nat -p tcp -d 10.42.0.19 -j DNAT --to-destination 10.42.0.10:80
```

Pada kasus ini kita menggunakan solusi Load Balancing untuk mendistribusikan koneksi. Untuk mengatasi masalah ini, kita menggunakan -A PREROUTING PREROUTING chain pada -t nat NAT table untuk mengubah destination IP yang awalnya menuju ke 10.42.0.19 DNS Server DORIKI menjadi ke 10.42.0.11:80 Server JORGE port 80 dan 10.42.0.10:80 Server MAINGATE port 80.















