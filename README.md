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

# .bashrc

### Paradis (DHCP Relay)
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.233.0.0/16
apt-get update
apt-get install isc-dhcp-relay -y
service isc-dhcp-relay start
```

### Tybur (DHCP Server)
```
echo 'nameserver 192.233.4.2' > /etc/resolv.conf
apt-get update
apt-get install isc-dhcp-server -y
```

### Fritz (DNS Server)
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

### Colossal (PHP Load Balancer)
```
echo 'nameserver 192.233.4.2' > /etc/resolv.conf
apt-get update
apt-get install bind9 -y
apt-get install apache2-utils -y
apt-get install nginx -y
apt-get install lynx -y

service nginx start
```

### Warhammer (Database Server)
```
echo 'nameserver 192.233.4.2' > /etc/resolv.conf
apt-get update
apt-get install mariadb-server -y

service mysql start
```

### Annie, Bertholdt, Reiner (Laravel Worker)
```
echo 'nameserver 192.233.4.2' > /etc/resolv.conf
apt-get update
apt-get install lynx -y
apt-get install mariadb-client -y
```

### Zeke, Erwin (Client)
```
apt update
apt install lynx -y
apt install htop -y
apt install apache2-utils -y
apt-get install jq -y
```

# Soal Praktikum 

## Soal 0
```
echo 'zone "marley.it33.com" {
    type master;
    file "/etc/bind/sites/marley.it33.com";
};
zone "eldia.it33.com" {
    type master;
    file "/etc/bind/sites/eldia.it33.com";
};' > /etc/bind/named.conf.local

mkdir -p /etc/bind/sites
cp /etc/bind/db.local /etc/bind/sites/marley.it33.com
cp /etc/bind/db.local /etc/bind/sites/eldia.it33.com

echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     marley.it33.com. root.marley.it33.com. (
                        2024102301      ; Serial
                        604800         ; Refresh
                        86400         ; Retry
                        2419200         ; Expire
                        604800 )       ; Negative Cache TTL
;
@       IN      NS      marley.it33.com.
@       IN      A       192.233.1.2    ; IP Annie
www     IN      CNAME   marley.it33.com.' > /etc/bind/sites/marley.it33.com

echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     eldia.it33.com. root.eldia.it33.com. (
                            2024102301         ; Serial
                            604800              ; Refresh
                            86400              ; Retry
                            2419200              ; Expire
                            604800 )            ; Negative Cache TTL
;
@       IN      NS      eldia.it33.com.
@       IN      A       192.233.2.2    ; IP Armin
www     IN      CNAME   eldia.it33.com.' > /etc/bind/sites/eldia.it33.com

echo 'options {
    directory "/var/cache/bind";

    forwarders {
        192.168.122.1;
    };

    // dnssec-validation auto;

    allow-query { any; };
    auth-nxdomain no;    # conform to RFC1035
    listen-on-v6 { any; };
};' > /etc/bind/named.conf.options

service bind9 restart
```

## Soal 1

## Soal 2-5
```
echo 'INTERFACESv4="eth0"' > /etc/default/isc-dhcp-server

echo 'subnet 192.233.1.0 netmask 255.255.255.0 {
#Soal 2 Range IP Marley
        range 192.233.1.05 192.233.1.25;
        range 192.233.1.50 192.233.1.100;
        option routers 192.233.1.1;
#Soal 4 DNS Server Fritz
        option broadcast-address 192.233.1.255;
        option domain-name-servers 192.233.4.2;
#Soal 5 Durasi DHCP Marley
        default-lease-time 1800;
        max-lease-time 5220;
}

subnet 192.233.2.0 netmask 255.255.255.0 {
#Soal 3 Range IP Eldia
        range 192.233.2.09 192.233.2.27;
        range 192.233.2.81 192.233.2.243;
        option routers 192.233.2.1;
#Soal 4 DNS Server Fritz
        option broadcast-address 192.233.2.255;
        option domain-name-servers 192.233.4.2;
#Soal 5 Durasi DHCP Eldia
        default-lease-time 360;
        max-lease-time 5220;
}

subnet 192.233.3.0 netmask 255.255.255.0 {
        option routers 192.233.3.1;
}

subnet 192.233.4.0 netmask 255.255.255.0 {
        option routers 192.233.4.1;
} ' > /etc/dhcp/dhcpd.conf

service isc-dhcp-server restart
```
