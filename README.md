# Jarkom-Modul-5-F03-2022
Jarkom-Modul-5-F03-2022

### Kelompok F03

| **No** | **Nama**                   | **NRP**    | **Pembagian Pekerjaan** |
| ------ | -------------------------- | ---------- | ----------------------- |
| 1      | Naufal Fabian Wibowo    | 05111940000223 |  |
| 2      | Angela Oryza Prabowo          | 5025201022 | Mengerjakan Lapres |
| 3      | Helmi Taqiyuddin | 5025201152 | Mengerjakan nomor 1-6 |

``` IP Kelompok F03 = 10.30``` 

**Topologi**

**Konfigurasi Node**

**Eden**
```
auto eth0
iface eth0 inet static
       address 10.30.0.10
       netmask 255.255.255.248
       gateway 10.30.0.9

       up echo nameserver 192.168.122.1 > /etc/resolv.conf
 ```

**WISE**
```
auto eth0
iface eth0 inet static
       address 10.30.0.11
       netmask 255.255.255.248
       gateway 10.30.0.9

       up echo nameserver 192.168.122.1 > /etc/resolv.conf
 ```

**SSS**
```
auto eth0
iface eth0 inet static
       address 10.30.0.19
       netmask 255.255.255.248
       gateway 10.30.0.17
       up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

**Garden**
```
auto eth0
iface eth0 inet static
       address 10.30.0.18
       netmask 255.255.255.248
       gateway 10.30.0.17

       up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

**Westalis**
```
auto eth0
iface eth0 inet static
        address 10.30.0.2
        netmask 255.255.255.252
        gateway 10.30.0.1

auto eth1
iface eth1 inet static
        address 10.30.0.9
        netmask 255.255.255.248

auto eth2
iface eth2 inet static
         address 10.30.0.129
         netmask 255.255.255.128

auto eth3
iface eth3 inet static
         address 10.30.4.1
         netmask 255.255.252.0
```

**Strix**
```
auto eth0
iface eth0 inet static
       address 192.168.122.2
       netmask 255.255.255.252
       gateway 192.168.122.1
       up echo nameserver 192.168.122.1 > /etc/resolv.conf

auto eth1
iface eth1 inet static
        address 10.30.0.5
        netmask 255.255.255.252

auto eth2
iface eth2 inet static
         address 10.30.0.1
         netmask 255.255.255.252
```

**Ostania**
```
auto eth0
iface eth0 inet static
          address 10.30.0.6
          netmask 255.255.255.252
          gateway 10.30.0.5

auto eth1
iface eth1 inet static
          address 10.30.0.17
          netmask 255.255.255.248

auto eth2
iface eth2 inet static
          address 10.30.2.1
          netmask 255.255.254.0

auto eth3
iface eth3 inet static
          address 10.30.1.1
          netmask 255.255.255.0
```

## Routing dan Konfigurasi DNS, Web server, DHCP Server, dan DHCP relay
[ Ostania ]
```
route add -net 0.0.0.0 netmask 0.0.0.0 gw 10.30.0.5
```
[Strix]
```
route add -net 10.30.0.8 netmask 255.255.255.248 gw 10.30.0.2
route add -net 10.30.0.128 netmask 255.255.255.128 gw 10.30.0.2
route add -net 10.30.4.0 netmask 255.255.252.0 gw 10.30.0.2

route add -net 10.30.2.0 netmask 255.255.254.0 gw 10.30.0.6
route add -net 10.30.1.0 netmask 255.255.255.0 gw 10.30.0.6
route add -net 10.30.0.16 netmask 255.255.255.248 gw 10.30.0.6
```
[Westalis]
```
route add -net 0.0.0.0 netmask 0.0.0.0 gw 10.30.0.1
```
[ Eden adalah DNS Server ]
```
apt-get update &
wait
apt-get install bind9 -y &
wait

echo '
options {
        directory "/var/cache/bind";
        forwarders {
                192.168.122.1;
        };
        // dnssec-validation auto;
        allow-query{any;};
        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};

' > /etc/bind/named.conf.options

service bind9 restart
service bind9 restart
```
[ WISE adalah DHCP Server ]
```
apt-get update &
wait
apt-get install isc-dhcp-server -y &
wait

echo 'INTERFACES="eth0"' > '/etc/default/isc-dhcp-server'

echo '
# Subnet A1
subnet 10.30.0.8 netmask 255.255.255.248 {
}

# Subnet A2
subnet 10.30.0.128 netmask 255.255.255.128 {
    range 10.30.0.130 10.30.0.255;
    option routers 10.30.0.129;
    option broadcast-address 10.30.0.255;

    option domain-name-servers 10.30.0.10;

    default-lease-time 360;
    max-lease-time 7200;
}

# Subnet A3
subnet 10.30.4.0 netmask 255.255.252.0 {
    range 10.30.4.2 10.30.7.255;
    option routers 10.30.4.1;
    option broadcast-address 10.30.7.255;

    option domain-name-servers 10.30.0.10;

    default-lease-time 360;
    max-lease-time 7200;
}

# Subnet A6
subnet 10.30.2.0 netmask 255.255.254.0 {
    range 10.30.2.2 10.30.3.255;
    option routers 10.30.2.1;
    option broadcast-address 10.30.3.255;

    option domain-name-servers 10.30.0.10;

    default-lease-time 360;
    max-lease-time 7200;
}

# Subnet A7
subnet 10.30.1.0 netmask 255.255.255.0 {
    range 10.30.1.2 10.30.1.255;
    option routers 10.30.1.1;
    option broadcast-address 10.30.1.255;

    option domain-name-servers 10.30.0.10;

    default-lease-time 360;
    max-lease-time 7200;
}' > /etc/dhcp/dhcpd.conf

service isc-dhcp-server restart
service isc-dhcp-server restart
```
[ Ostania sebagai DHCP Relay ]
```
apt-get update &
wait
apt-get install isc-dhcp-relay -y &
wait

echo '
SERVERS="10.30.0.11"
INTERFACES="eth0 eth1 eth2 eth3"
OPTIONS=
' > /etc/default/isc-dhcp-relay

service isc-dhcp-relay restart
service isc-dhcp-relay restart
```
[ Westalis sebagai DHCP Relay ]
```
apt-get update &
wait
apt-get install isc-dhcp-relay -y &
wait

echo '
SERVERS="10.30.0.11"
INTERFACES="eth0 eth1 eth2 eth3"
OPTIONS=
' > /etc/default/isc-dhcp-relay

service isc-dhcp-relay restart
service isc-dhcp-relay restart
```

## Soal Nomor 1
Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Strix menggunakan iptables, tetapi Loid tidak ingin menggunakan MASQUERADE.

### Jawaban Nomor 1
[ Strix ]
```
iptables -t nat -A POSTROUTING -o eth0 -j SNAT -s 10.30.0.0/21 --to-source 192.168.122.2
```

### Hasil Nomor 1
<picture>
     <img alt="Screenshoot hasil No 1." src="https://github.com/angelaoryza/Jarkom-Modul-5-F03-2022/blob/main/dokumentasi/no_1.jpg">
</picture>

## Soal Nomor 2
Kalian diminta untuk melakukan drop semua TCP dan UDP dari luar Topologi kalian pada server yang merupakan DHCP Server demi menjaga keamanan.

### Jawaban Nomor 2
[ Strix ]
```
iptables -N LOGGING
iptables -A FORWARD -i eth0 -p tcp -d 10.30.0.11 -j LOGGING
iptables -A FORWARD -i eth0 -p udp -d 10.30.0.11 -j LOGGING

service rsyslog restart
```
### Hasil Nomor 2
<picture>
     <img alt="Screenshoot hasil No 2." src="https://github.com/angelaoryza/Jarkom-Modul-5-F03-2022/blob/main/dokumentasi/no_4.jpg">
</picture>
    
## Soal Nomor 3
Loid meminta kalian untuk membatasi DHCP dan DNS Server hanya boleh menerima maksimal 2 koneksi ICMP secara bersamaan menggunakan iptables, selebihnya didrop.

### Jawaban Nomor 3
[ Eden dan WISE ]
```
iptables -N LOGGING
iptables -A INPUT -p icmp -m connlimit --connlimit-above 2 --connlimit-mask 0 -j LOGGING
iptables -A LOGGING -j LOG --log-prefix "IPTables-Dropped: "
iptables -A LOGGING -j DROP
```
### Hasil Nomor 3
<picture>
     <img alt="Screenshoot hasil No 3." src="https://github.com/angelaoryza/Jarkom-Modul-5-F03-2022/blob/main/dokumentasi/no_3-1.jpg">
</picture>
<picture>
     <img alt="Screenshoot hasil No 3." src="https://github.com/angelaoryza/Jarkom-Modul-5-F03-2022/blob/main/dokumentasi/no_3-2.jpg">
</picture>
<picture>
     <img alt="Screenshoot hasil No 3." src="https://github.com/angelaoryza/Jarkom-Modul-5-F03-2022/blob/main/dokumentasi/no_3-3.jpg">
</picture>

## Soal Nomor 4
Akses menuju Web Server hanya diperbolehkan disaat jam kerja yaitu Senin sampai Jumat pada pukul 07.00 - 16.00.

### Jawaban Nomor 4
[ Garden dan SSS ]
```
iptables -A INPUT -m time --timestart 07:00 --timestop 16:00 --weekdays Mon,Tue,Wed,Thu,Fri -j ACCEPT
```

### Hasil Nomor 4
<picture>
     <img alt="Screenshoot hasil No 4." src="https://github.com/angelaoryza/Jarkom-Modul-5-F03-2022/blob/main/dokumentasi/no_2.jpg">
</picture>

## Soal Nomor 5
Karena kita memiliki 2 Web Server, Loid ingin Ostania diatur sehingga setiap request dari client yang mengakses Garden dengan port 80 akan didistribusikan secara bergantian pada SSS dan Garden secara berurutan dan request dari client yang mengakses SSS dengan port 443 akan didistribusikan secara bergantian pada Garden dan SSS secara berurutan.

### Jawaban Nomor 5
[ Ostania ]
```
iptables -A PREROUTING -t nat -p tcp -d 10.30.0.19 --dport 80 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 10.30.0.19:80
iptables -A PREROUTING -t nat -p tcp -d 10.30.0.19 --dport 80 -j DNAT --to-destination 10.30.0.18:80
iptables -A PREROUTING -t nat -p tcp -d 10.30.0.18 --dport 443 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 10.30.0.18:443
iptables -A PREROUTING -t nat -p tcp -d 10.30.0.18 --dport 443 -j DNAT --to-destination 10.30.0.19:443
```
### Hasil Nomor 5
<picture>
     <img alt="Screenshoot hasil No 5." src="https://github.com/angelaoryza/Jarkom-Modul-5-F03-2022/blob/main/dokumentasi/no_5.jpg">
</picture>

## Soal Nomor 6
Karena Loid ingin tau paket apa saja yang di-drop, maka di setiap node server dan router ditambahkan logging paket yang di-drop dengan standard syslog level.

### Jawaban Nomor 6
```
iptables -N LOGGING
iptables -A INPUT -j LOGGING
iptables -A LOGGING -j LOG --log-prefix "IPTables-Rejected: "
iptables -A LOGGING -j REJECT
```
### Hasil Nomor 6
<picture>
     <img alt="Screenshoot hasil No 6." src="https://github.com/angelaoryza/Jarkom-Modul-5-F03-2022/blob/main/dokumentasi/no_6.jpg">
</picture>
