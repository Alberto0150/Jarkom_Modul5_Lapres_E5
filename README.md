# Jarkom_Modul5_Lapres_E5
Lapres Modul 5 Jarkom

### Nama Anggota Kelompok :
### 1. Alberto Sanjaya 05111840000150  

### Soal A

Membuat topologi jaringan sesuai dengan rancangan yang diberikan pada gambar di atas. Sekalian membuat ```bye.sh```nya.
Topologi.sh

```
# Switch
uml_switch -unix switch0 > /dev/null < /dev/null & #A0
uml_switch -unix switch1 > /dev/null < /dev/null & #A1
uml_switch -unix switch2 > /dev/null < /dev/null & #A2
uml_switch -unix switch3 > /dev/null < /dev/null & #A3
uml_switch -unix switch4 > /dev/null < /dev/null & #A4
uml_switch -unix switch5 > /dev/null < /dev/null & #A5

# Router
xterm -T SURABAYA -e linux ubd0=SURABAYA,jarkom umid=SURABAYA eth0=tuntap,,,10.151.70.25 eth1=daemon,,,switch0 eth2=daemon,,,switch3 mem=96M &
xterm -T BATU -e linux ubd0=BATU,jarkom umid=BATU eth0=daemon,,,switch3 eth1=daemon,,,switch2 eth2=daemon,,,switch5 mem=96M &
xterm -T KEDIRI -e linux ubd0=KEDIRI,jarkom umid=KEDIRI eth0=daemon,,,switch0 eth1=daemon,,,switch1 eth2=daemon,,,switch4 mem=96M &

# Server
xterm -T PROBOLINGGO -e linux ubd0=PROBOLINGGO,jarkom umid=PROBOLINGGO eth0=daemon,,,switch1 mem=128M &
xterm -T MADIUN -e linux ubd0=MADIUN,jarkom umid=MADIUN eth0=daemon,,,switch1 mem=128M &
xterm -T MALANG -e linux ubd0=MALANG,jarkom umid=MALANG eth0=daemon,,,switch2 mem=128M &
xterm -T MOJOKERTO -e linux ubd0=MOJOKERTO,jarkom umid=MOJOKERTO eth0=daemon,,,switch2 mem=128M &

# Klien 1
xterm -T GRESIK -e linux ubd0=GRESIK,jarkom umid=GRESIK eth0=daemon,,,switch4 mem=96M &
xterm -T SIDOARJO -e linux ubd0=SIDOARJO,jarkom umid=SIDOARJO eth0=daemon,,,switch5 mem=96M &
```
  
bye.sh

```
uml_mconsole SURABAYA halt
uml_mconsole MALANG halt
uml_mconsole MOJOKERTO halt
uml_mconsole SIDOARJO halt
uml_mconsole GRESIK halt
uml_mconsole BATU halt
uml_mconsole KEDIRI halt
uml_mconsole PROBOLINGGO halt
uml_mconsole MADIUN halt

```

### Soal B

Membuat subnetting dengan teknik **CIDR** atau **VLSM**. Kelompok kami menggunakan teknik **VLSM**.  

- Pembagian subnet dengan teknik VLSM  

![subnet](ss/vlsm.png)

- IP Tree

![route](ss/route.png)  

- Setting **Interfaces** tiap UML

*UML Surabaya (Router)*

```
auto lo
iface lo inet loopback

auto eth0 
iface eth0 inet static
address 10.151.70.26
netmask 255.255.255.252
gateway 10.151.70.25


auto eth1
iface eth1 inet static
address 192.168.0.5
netmask 255.255.255.252


auto eth2
iface eth2 inet static
address 192.168.0.1
netmask 255.255.255.252
```

*UML Kediri (Router)*

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.0.6
netmask 255.255.255.252
gateway 192.168.0.5

auto eth1
iface eth1 inet static
address 192.168.0.9
netmask 255.255.255.248

auto eth2
iface eth2 inet static
address 192.168.2.1
netmask 255.255.255.0
```

*UML Batu (Router)*

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.0.2
netmask 255.255.255.252
gateway 192.168.0.1

auto eth1
iface eth1 inet static
address 10.151.71.49
netmask 255.255.255.248

auto eth2
iface eth2 inet static
address 192.168.1.1
netmask 255.255.252.0
```

*UML Sidoarjo (Client)*

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet dhcp
address 192.168.1.2
netmask 255.255.255.0
gateway 192.168.1.1
```

*UML Gresik (Client)*

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet dhcp
address 192.168.2.2
netmask 255.255.255.0
gateway 192.168.2.1
```

*UML Probolinggo (Server)*

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.0.10
netmask 255.255.255.248
gateway 192.168.0.9
```

*UML Mojokerto (Server)*

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 10.151.71.51
netmask 255.255.255.248
gateway 10.151.71.49
```

*UML Malang (Server)*

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 10.151.71.50
netmask 255.255.255.248
gateway 10.151.71.49
```

*UML Madiun (Server)*

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.0.11
netmask 255.255.255.248
gateway 192.168.0.9
```

### Soal C

Melakukan routing agar setiap perangkat pada jaringan tersebut dapat terhubung.

- Routing pada UML Surabaya

```
ip route add 10.151.71.48/29 via 192.168.0.1
ip route add 192.168.1.0/24 via 192.168.0.1
ip route add 192.168.2.0/24 via 192.168.0.5
ip route add 192.168.0.8/29 via 192.168.0.5
```

### Soal D

Memberikan ip pada subnet SIDOARJO dan GRESIK secara dinamis menggunakan bantuan DHCP SERVER (Selain subnet tersebut menggunakan ip static). Kemudian setting DHCP RELAY pada router yang menghubungkannya.

- *UML Surabaya, Batu, dan Kediri*

  1. Buka file ```nano /etc/sysctl.conf```
  2. Tambahkan :

  ```
  net.ipv4.ip_forward=1
  net.ipv4.conf.all.accept_source_route = 1
  ```
  3. Lakukan ```sysctl -p```
  4. Install DHCP RELAY : ```apt-get install isc-dhcp-relay -y```
  5. Buka filenya ```nano /etc/default/isc-dhcp-relay```

  

- *UML Mojokerto*

  1. Install DHCP : ```apt-get install isc-dhcp-server -y```
  2. Buka filenya ```nano /etc/default/isc-dhcp-server```
  3. Menambahkan ```INTERFACES="eth0"```
  4. Buka file ```nano /etc/dhcp/dhcpd.conf```
  5. Tambahkan :

  ```  
    subnet 10.151.71.48 netmask 255.255.255.248 {
        option routers 10.151.71.49;
        option broadcast-address 10.151.71.55;
    }
    subnet 192.168.1.0 netmask 255.255.255.0 {
        range 192.168.1.2 192.168.1.203;
        option routers 192.168.1.1;
        option broadcast-address 192.168.1.255;
        option domain-name-servers 10.151.71.50;
        option domain-name-servers 202.46.129.2;
        default-lease-time 600;
        max-lease-time 7200;
    }
    subnet 192.168.2.0 netmask 255.255.255.0 {
        range 192.168.2.2 192.168.2.203;
        option routers 192.168.2.1;
        option broadcast-address 192.168.2.255;
        option domain-name-servers 10.151.71.50;
        option domain-name-servers 202.46.129.2;
        default-lease-time 600;
        max-lease-time 7200;
    }
  ```

### Nomor 1

Agar topologi yang dibuat dapat mengakses keluar, kita mengkonfigurasi SURABAYA menggunakan iptables, namun tidak menggunakan MASQUERADE.

- Pada *UML Surabaya* tambahkan perintah :
  
  ```
  iptables -t nat -A POSTROUTING -s 192.168.0.0/22 -o eth0 -j SNAT --to-source 10.151.70.26
  ```

- Testing dengan cara ping **google.com** di UML Surabaya  
    ![1](ss/1.JPG)  
  

### Nomor 2

Mendrop semua akses SSH dari luar Topologi (UML) kita pada server yang memiliki ip DMZ (DHCP dan DNS SERVER) pada SURABAYA demi menjaga keamanan.

- Pada *UML Surabaya* tambahkan perintah :

  ```
  iptables -A FORWARD -d 10.151.71.48/29 -i eth0 -p tcp --dport 22 -j DROP
  ```
  
- Testing

  1. Install netcat di *UML Mojokerto* dan *UML Malang* : ```apt-get install netcat```
  2. Pada putty Yudhistira ```nc 10.151.77.131 22```
  3. Pada UML Mojokerto dan Malang, ketikkan : ```nc -l -p <nomor_port>```
  
  ![2](ss/2.JPG)  
  
### Nomor 3

Membatasi DHCP dan DNS server hanya boleh menerima maksimal 3 koneksi ICMP secara bersamaan yang berasal dari mana saja menggunakan iptables pada masing masing server, selebihnya akan di DROP.

- Pada *UML Mojokerto* dan *UML Malang* tambahkan perintah :

  ```
  iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
  ```

- Testing :

  1. Melakukan ping ke IP MALANG atau IP MOJOKERTO di 4 UML.
  2. Jika saat di UML ke 4 tidak bisa melakukan ping, maka berhasil.
  
  ![3](ss/3.JPG)  
  
### Nomor 4

Akses dari subnet SIDOARJO hanya diperbolehkan pada pukul 07.00 - 17.00 pada hari Senin sampai Jumat. Selain itu akan diREJECT.

Pada *UML Malang* tambahkan :

```
iptables -A INPUT -s 192.168.1.0/24 -m time --timestart 07:00 --timestop 17:00 --days Mon,Tue,Wed,Thu,Fri -j ACCEPT
iptables -A INPUT -s 192.168.1.0/24 -j REJECT
```

- Testing pada *UML Gresik* dan *UML Malang* :

  1. Ubah tanggal dan jam hari ini dengan perintah ```date -s '2020-12-27 :::'```
  2. Cek tanggal sudah benar atau belum dengan perintah ```date```
  
  ![4](ss/4.JPG)  
  
### Nomor 5

Akses dari subnet GRESIK hanya diperbolehkan pada pukul 17.00 hingga pukul 07.00 setiap harinya. Selain itu akan diREJECT.

Pada *UML Malang* tambahkan :

```
iptables -A INPUT -s 192.168.2.0/24 -m time --timestart 07:01 --timestop 16:59 -j REJECT
```

- Testing pada *UML Gresik* dan *UML Malang* :

  1. Ubah tanggal dan jam hari ini dengan perintah ```date -s "2020-12-28 16:10:10"```
  2. Cek tanggal sudah benar atau belum dengan perintah ```date```
  
![5.1](ss/5.1.JPG)  
![5.2](ss/5.2.JPG)  

### Nomor 6

SURABAYA disetting sehingga setiap request dari client yang mengakses DNS Server akan didistribusikan secara bergantian pada PROBOLINGGO port 80 dan MADIUN port 80.

- Pada *UML Surabaya* :

```
iptables -A PREROUTING -t nat -p tcp -d 10.151.77.26 --dport 80 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.168.0.10:80
iptables -A PREROUTING -t nat -p tcp -d 10.151.77.26 --dport 80 -j DNAT --to-destination 192.168.0.11:80

iptables -t nat -A POSTROUTING -p tcp -d 192.168.0.10 --dport 80 -j SNAT --to-source 10.151.77.26:80
iptables -t nat -A POSTROUTING -p tcp -d 192.168.0.11 --dport 80 -j SNAT --to-source 10.151.77.26:80

```

- Testing

1. Install netcat pada *UML Malang*, *UML Probolinggo*, *UML Madiun*, *UML Surabaya*, dan *UML Gresik (Client)*  : ```apt-get install netcat```
2. Pada UML Madiun ketikkan : ```nc -l -p 80```
3. Pada UML Probolinggo ketikkan : ```nc -l -p 80```
4. Pada UML Client (Gresik) ketikkan : ```nc ipMalang 80```
5. Ketik bebas apapun di UML Gresik nanti muncul di UML Madiun/ Probolinggo  
![6](ss/6.JPG)  

### Nomor 7

Semua paket didrop oleh firewall (dalam topologi) tercatat dalam log pada setiap UML yang memiliki aturan drop.

- Pada *UML Surabaya* tambahkan :

```
iptables -N LOGGING
iptables -A INPUT -j LOGGING
iptables -A OUTPUT -j LOGGING
iptables -A LOGGING -j LOG --log-prefix "IPTables-Dropped: " --log-level 4
iptables -A LOGGING -j DROP
```

- Testing

1. Jalankan iptablesnya
2. Pindah directory ke ```/var/log/messages```

![7](ss/7.JPG)  
---
Kendala yang dialami:  
Mengalami error pada router Kediri yang mengarah ke Gresik sehingga untuk no 5 tidak dapat dilaksanakan
