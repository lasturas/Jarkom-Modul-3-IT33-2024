# Write Up-Jarkom-Modul-3-Kelompok-IT33

| Nama | NRP |
|----------|----------|
| Ricko Mianto Jaya Saputra | 5027231031 |
| Tsaldia Hukma Cita | 5027231036 | 

# Daftar Isi
1. [Topology](#topology)
2. [Konfigurasi](#konfigurasi)
3. [Soal](#soal-praktikum)

# Topology 
![image](https://github.com/user-attachments/assets/aadfaf11-db1a-464a-ab1b-cf53a9d91c9b)


dengan ketentuan sebagai berikut:

| **Node**       | **Category**               | **IP Configuration** |
|----------------|----------------------------|----------------------|
| Paradis        | Router (DHCP Relay)         | Dynamic              |
| Tybur          | DHCP Server                 | Static               |
| Fritz          | DNS Server                  | Static               |
| Warhammer      | Database Server             | Static               |
| Beast          | Load Balancer (Laravel)     | Static               |
| Colossal       | Load Balancer (PHP)         | Static               |
| Annie          | Laravel Worker              | Static               |
| Bertholdt      | Laravel Worker              | Static               |
| Reiner         | Laravel Worker              | Static               |
| Armin          | PHP Worker                  | Static               |
| Eren           | PHP Worker                  | Static               |
| Mikasa         | PHP Worker                  | Static               |
| Zeke           | Client                      | Dynamic              |
| Erwin          | Client                      | Dynamic              |

# Konfigurasi 
| Kelompok | Prefix IP |
|----------|----------|
| IT33 | 192.233 |

### Paradis - DHCP Relay
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 192.233.1.0
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 192.233.2.0
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 192.233.3.0
	netmask 255.255.255.0

auto eth4
iface eth4 inet static
	address 192.233.4.0
	netmask 255.255.255.0
```

### Tybur - DHCP Server
```
auto eth0
iface eth0 inet static
    address 192.233.4.3
    netmask 255.255.255.0
    gateway 192.233.4.0
```

### Annie - Laravel Worker
```
auto eth0
iface eth0 inet static
	address 192.233.1.2
	netmask 255.255.255.0
	gateway 192.233.1.0
```

### Bertholdt - Laravel Worker
```
auto eth0
iface eth0 inet static
    address 192.233.1.3
    netmask 255.255.255.0
    gateway 192.233.1.0
```

### Reiner - Laravel Worker
```
auto eth0
iface eth0 inet static
    address 192.233.1.4
    netmask 255.255.255.0
    gateway 192.233.1.0
```

### Armin - PHP Worker
```
auto eth0
iface eth0 inet static
    address 192.233.2.2
    netmask 255.255.255.0
    gateway 192.233.2.0
```

### Eren - PHP Worker
```
auto eth0
iface eth0 inet static
    address 192.233.2.3
    netmask 255.255.255.0
    gateway 192.233.2.0
```

### Mikasa - PHP Worker
```
auto eth0
iface eth0 inet static
    address 192.233.2.4
    netmask 255.255.255.0
    gateway 192.233.2.0
```

### Beast - LoadBalancer Laravel
```
auto eth0
iface eth0 inet static
    address 192.233.3.2
    netmask 255.255.255.0
    gateway 192.233.3.0
```

### Colossal - LoadBalancer PHP
```
auto eth0
iface eth0 inet static
    address 192.233.3.3
    netmask 255.255.255.0
    gateway 192.233.3.0
```

### Warhammer - Database
```
auto eth0
iface eth0 inet static
    address 192.233.3.4
    netmask 255.255.255.0
    gateway 192.233.3.0
```

### Fritz - DNS Server
```
auto eth0
iface eth0 inet static
    address 192.233.4.2
    netmask 255.255.255.0
    gateway 192.233.4.0
```

### Zeke - Client
```
auto eth0
iface  eth0 inet static
  address 192.233.1.4
  netmask 255.255.255.0
  gateway 192.233.2.0
```

### Erwin - Client
```
auto eth0
iface  eth0 inet static
  address 192.233.2.4
  netmask 255.255.255.0
  gateway 192.233.2.0
```
## Config Nameserver
Buka terminal Paradise, masukkan command untuk masuk ke bash `nano /root/.bashrc` dan inputkan kode berikut untuk NAT dengan menggunakan prefix IP kelompok
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.233.0.0/16
```

Lalu untuk mengecek nameserver kirimkan kode berikut kepada node Paradise 
```
cat /etc/resolv.conf
```
Maka akan muncul informasi seperti berikut ini `nameserver 192.168.122.1`

Setelah mengetahui IP nameserver, maka pada semua node buka `nano /etc/resolv.conf` dan tambahkan kode berikut, kecuali pada node Zeke dan Erwin (Client)
```
nameserver 192.168.122.1
```

Sedangkan pada node Zeke dan Erwin (Client) tambahkan kode berikut 
```
up echo nameserver 192.233.4.1
up echo nameserver 192.168.122.1
```

# .bashrc

## Paradis (DHCP Relay)
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.233.0.0/16
apt-get update
apt-get install isc-dhcp-relay -y
service isc-dhcp-relay start
```

## Tybur (DHCP Server)
```
echo 'nameserver 192.233.4.2' > /etc/resolv.conf
apt-get update
apt-get install isc-dhcp-server -y
```

## Fritz (DNS Server)
```
echo 'nameserver 192.168.122.1' > /etc/resolv.conf
apt-get update
apt-get install bind9 -y

echo 'options {
        directory "/var/cache/bind";

        forwarders {
                192.168.122.1;
        };

        // dnssec-validation auto;
        allow-query{any;};
        auth-nxdomain no;
        listen-on-v6 { any; };
}; ' >/etc/bind/named.conf.options

service bind9 restart
```

## Armin, Eren, Mikasa (PHP Worker)
```
echo 'nameserver 192.233.4.2' > /etc/resolv.conf
apt-get update
apt-get install lynx -y
apt-get install wget -y
apt-get install unzip -y
apt-get install nginx -y
apt install software-properties-common -y
apt install php7.3 -y
apt install php7.3-fpm -y
```

## Colossal (PHP Load Balancer)
```
echo 'nameserver 192.233.4.2' > /etc/resolv.conf
apt-get update
apt-get install bind9 -y
apt-get install apache2-utils -y
apt-get install nginx -y
apt-get install lynx -y

service nginx start
```

## Warhammer (Database Server)
```
echo 'nameserver 192.233.4.2' > /etc/resolv.conf
apt-get update
apt-get install mariadb-server -y

service mysql start
```

## Annie, Bertholdt, Reiner (Laravel Worker)
```
echo 'nameserver 192.233.4.2' > /etc/resolv.conf
apt-get update
apt-get install lynx -y
apt-get install mariadb-client -y
```

## Zeke, Erwin (Client)
```
apt update
apt install lynx -y
apt install htop -y
apt install apache2-utils -y
apt-get install jq -y
```

# Soal Praktikum 
