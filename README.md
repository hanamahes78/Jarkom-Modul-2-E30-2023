# **Praktikum 2 Jaringan Komputer**
<div align=justify>

Berikut repository dari Kelompok E30 Praktikum Modul 2 Jaringan Komputer.

# **Anggota Kelompok**

| Nama                      | NRP        | Kelas                |
| ------------------------- | ---------- | ----------------     |
| Hana Maheswari            | 5025211182 | Jaringan Komputer E  |
| Meyroja Jovancha Firoos   | 5025211204 | Jaringan Komputer E  |

# **Dokumentasi dan Penjelasan Soal**
<div align=justify>

## **Soal Nomor 1**
Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi dengan pembagian.
## **Penyelesaian Soal Nomor 1**
Berikut adalah topologi yang digunakan. 

![05](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/aff95ddb-9324-4b6a-a168-568a8598a65b)
### Konfigurasi
- Pandudewanata
    ```
    auto eth0
    iface eth0 inet dhcp

    auto eth1
    iface eth1 inet static
	    address 192.221.1.1
	    netmask 255.255.255.0

    auto eth2
    iface eth2 inet static
	    address 192.221.2.1
	    netmask 255.255.255.0

    auto eth3
    iface eth3 inet static
	    address 192.221.3.1
	    netmask 255.255.255.0
    ```
- Nakula
    ```
    auto eth0
    iface eth0 inet static
	    address 192.221.1.2
	    netmask 255.255.255.0
	    gateway 192.221.1.1
    ```
- Sadewa
    ```
    auto eth0
    iface eth0 inet static
	    address 192.221.1.3
	    netmask 255.255.255.0
	    gateway 192.221.1.1
    ```
- Yudhistira
    ```
    auto eth0
    iface eth0 inet static
	    address 192.221.2.2
	    netmask 255.255.255.0
	    gateway 192.221.2.1
    ```
- Werkudara
    ```
    auto eth0
    iface eth0 inet static
	    address 192.221.2.3
	    netmask 255.255.255.0
	    gateway 192.221.2.1
    ```
- Prabukusuma
    ```
    auto eth0
    iface eth0 inet static
	    address 192.221.3.2
	    netmask 255.255.255.0
	    gateway 192.221.3.1
    ```
- Abimanyu
    ```
    auto eth0
    iface eth0 inet static
	    address 192.221.3.3
	    netmask 255.255.255.0
	    gateway 192.221.3.1
    ```
- Wisanggeni
    ```
    auto eth0
    iface eth0 inet static
	    address 192.221.3.4
	    netmask 255.255.255.0
	    gateway 192.221.3.1
    ```
- Arjuna
    ```
    auto eth0
    iface eth0 inet static
	    address 192.221.3.5
	    netmask 255.255.255.0
	    gateway 192.221.3.1
    ```

### Script Awal
Menambahkan pada `/root/.bashrc` masing-masing node.
- Router (Pandudewanata)
  ```
    # ~/.bashrc: executed by bash(1) for non-login shells.
    # see /usr/share/doc/bash/examples/startup-files (in the package bash-doc)
    # for examples
    ...
    iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.221.0.0/16
  ```
- Client (Nakula, Sadewa)
  ```
    # ~/.bashrc: executed by bash(1) for non-login shells.
    # see /usr/share/doc/bash/examples/startup-files (in the package bash-doc)
    # for examples
    ...
    echo -e '
    nameserver 192.221.2.2
    nameserver 192.221.2.3
    nameserver 192.168.122.1
    ' > /etc/resolv.conf

    apt-get update
    apt-get install dnsutils -y
    apt-get install lynx -y
  ```
- Master & Slave (Yudhistira, Werkudara)
  ```
    # ~/.bashrc: executed by bash(1) for non-login shells.
    # see /usr/share/doc/bash/examples/startup-files (in the package bash-doc)
    # for examples
    ...
    echo 'nameserver 192.168.122.1' > /etc/resolv.conf

    apt-get update
    apt-get install bind9 -y
  ```
- Web Server (Prabakusuma, Abimanyu, Wisanggeni)
  ```
    # ~/.bashrc: executed by bash(1) for non-login shells.
    # see /usr/share/doc/bash/examples/startup-files (in the package bash-doc)
    # for examples
    ...
    echo "nameserver 192.168.122.1" > /etc/resolv.conf
  
    apt-get update
    apt install nginx php php-fpm -y
  ```
- Load Balancer (Arjuna)
  ```
    # ~/.bashrc: executed by bash(1) for non-login shells.
    # see /usr/share/doc/bash/examples/startup-files (in the package bash-doc)
    # for examples
    ...
    echo "nameserver 192.168.122.1" > /etc/resolv.conf
  
    apt-get update
    apt-get install nginx -y
  ```

### Testing
Setelah melakukan konfigurasi, akan dilakukan pengecekan internet untuk semua node dengan melakukan ping terhadap `google.com`.
> Script dijalankan dengan command `bash no1.sh`
- Semua Node
  ```
  ping google.com -c 3
  ```
![1 1](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/82241736-9586-44e4-89d9-55109f124f4f)
![1 2](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/9f975723-6571-4e75-a763-d2ee8c5ec118)

## **Soal Nomor 2**
Buatlah website utama pada node arjuna dengan akses ke arjuna.yyy.com dengan alias www.arjuna.yyy.com dengan yyy merupakan kode kelompok.
## **Penyelesaian Soal Nomor 2**
Dilakukan konfigurasi `/etc/bind/named.conf.local` pada node Yudhistira dengan domain arjuna.e30.com. Setelah itu dibuat direktori `/etc/bind/arjuna.e30`. Kemudian dibuat file `arjuna.e30.com` pada direktori tersebut dan file dilakukan konfigurasi (setelah command mkdir). Setelah selesai bind9 direstart menggunakan command `service bind9 restart`.
> Script dijalankan pada **root node Yudhistira** dengan command `bash no2.sh`
- Yudhistira
  ```
	echo -e '
	zone "arjuna.e30.com" {
        	type master;
       		file "/etc/bind/arjuna.e30/arjuna.e30.com";
	};
 	' > /etc/bind/named.conf.local

	mkdir /etc/bind/arjuna.e30

	echo -e '
	;
	; BIND data file for local loopback interface
	;
	$TTL    604800
	@       IN      SOA     arjuna.e30.com. root.arjuna.e30.com. (
	                      	      2     	; Serial
	                         604800         ; Refresh
	                          86400         ; Retry
	                        2419200         ; Expire
	                         604800 )       ; Negative Cache TTL
	;
	@       IN      NS      arjuna.e30.com.
	@       IN      A       192.221.3.5	; IP Arjuna 
	www     IN      CNAME   arjuna.e30.com. ; Alias
	@       IN      AAAA    ::1
	' > /etc/bind/arjuna.e30/arjuna.e30.com

	service bind9 restart
  ```
  
### Testing
Pada node client akan dicek dengan melakukan test alias (CNAME) dan test ping ke domain arjuna.e30.com.
> Script dijalankan pada **root node Nakula** dengan command `bash no2.sh`
- Nakula
  ```
  host -t CNAME www.arjuna.e30.com
  ping arjuna.e30.com -c 3
  ```
![2 1](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/0c5cfd7c-4741-476e-b187-52e3891b795d)
![2 2](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/88285973-e995-40f3-ad76-136868075704)

## **Soal Nomor 3**
Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke abimanyu.yyy.com dan alias www.abimanyu.yyy.com.
## **Penyelesaian Soal Nomor 3**
Sama dengan cara sebelumnya, dilakukan konfigurasi pada node Yudhistira dengan domain abimanyu.e30.com. Setelah itu dibuat file `abimanyu.e30.com` pada direktori tersebut dan disesuaikan.
> Script dijalankan pada **root node Yudhistira** dengan command `bash no3.sh`
- Yudhistira
  ```
	echo -e '
	zone "abimanyu.e30.com" {
        	type master;
        	file "/etc/bind/abimanyu.e30/abimanyu.e30.com";
	};
 	' > /etc/bind/named.conf.local

	mkdir /etc/bind/abimanyu.e30

	echo -e '
	;
	; BIND data file for local loopback interface
	;
	$TTL    604800
	@       IN      SOA     abimanyu.e30.com. root.abimanyu.e30.com. (
	                      	      2     	; Serial
	                         604800         ; Refresh
	                          86400         ; Retry
	                        2419200         ; Expire
	                         604800 )       ; Negative Cache TTL
	;
	@       IN      NS      abimanyu.e30.com.
	@       IN      A       192.221.3.3	; IP Abimanyu 
	www     IN      CNAME   abimanyu.e30.com. ; Alias
	@       IN      AAAA    ::1
	' > /etc/bind/abimanyu.e30/abimanyu.e30.com

	service bind9 restart
  ```
  
### Testing
Pada node client akan dicek dengan melakukan test alias (CNAME) dan test ping ke domain abimanyu.e30.com.
> Script dijalankan pada **root node Nakula** dengan command `bash no3.sh`
- Nakula
  ```
  host -t CNAME www.abimanyu.e30.com
  ping abimanyu.e30.com -c 3
  ```
![3 1](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/9f9a7be3-0714-44a6-ab9c-0b303b949610)
![3 2](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/79e11d9b-7953-4e63-9d83-a5c5b48c55f0)

## **Soal Nomor 4**
Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain parikesit.abimanyu.yyy.com yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.
## **Penyelesaian Soal Nomor 4**
Untuk membuat subdomain akan ditambahkan `parikesit IN A 192.221.3.3` pada file /etc/bind/abimanyu.e30/abimanyu.e30.com. Kemudian direstart menggunakan command `service bind9 restart`.
> Script dijalankan pada **root node Yudhistira** dengan command `bash no4.sh`
- Yudhistira
  ```
	echo -e '
	;
	; BIND data file for local loopback interface
	;
	$TTL    604800
	@       IN      SOA     abimanyu.e30.com. root.abimanyu.e30.com. (
	                      	      2     	; Serial
	                         604800         ; Refresh
	                          86400         ; Retry
	                        2419200         ; Expire
	                         604800 )       ; Negative Cache TTL
	;
	@		IN      NS      abimanyu.e30.com.
	@		IN      A       192.221.3.3		; IP Abimanyu 
	www		IN      CNAME   abimanyu.e30.com.	; Alias
	parikesit	IN      A       192.221.3.3		; IP Abimanyu
	@		IN      AAAA    ::1
	' > /etc/bind/abimanyu.e30/abimanyu.e30.com

	service bind9 restart
  ```

### Testing
Pada node client akan dicek dengan melakukan test ping ke domain parikesit.abimanyu.e30.com.
> Script dijalankan pada **root node Nakula** dengan command `bash no4.sh`
- Nakula
  ```
  ping parikesit.abimanyu.e30.com -c 3
  ```
![4 1](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/ae5276e0-b00b-4122-9cf9-d4f990203b17)
![4 2](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/0ec04158-5eee-4317-be38-83922c637054)

## **Soal Nomor 5**
Buat juga reverse domain untuk domain utama. (Abimanyu saja yang direverse)
## **Penyelesaian Soal Nomor 5**
Untuk membuat reverse domain diperlukan tambahan zone pada file `/etc/bind/named.conf.local` disesuaikan ip yang direverse. Kemudian dibuat file pada direktori `/etc/bind/reverse` dengan nama file "3 byte reverse ip Abimanyu".in-addr.arpa. Setelah selesai bind9 direstart menggunakan command `service bind9 restart`.
> Script dijalankan pada **root node Yudhistira** dengan command `bash no5.sh`
- Yudhistira
  ```
	echo -e '
	zone "abimanyu.e30.com" {
        	type master;
        	file "/etc/bind/abimanyu.e30/abimanyu.e30.com";
	};

	zone "3.221.192.in-addr.arpa" {
    		type master;
    		file "/etc/bind/reverse/3.221.192.in-addr.arpa";
	};
 	' > /etc/bind/named.conf.local

	mkdir /etc/bind/reverse

	echo -e '
	;
	; BIND data file for local loopback interface
	;
	$TTL    604800
	@       IN      SOA     abimanyu.e30.com. root.abimanyu.e30.com. (
				      2		; Serial
	                         604800         ; Refresh
	                          86400         ; Retry
	                        2419200         ; Expire
	                         604800 )       ; Negative Cache TTL
	;
	3.221.192.in-addr.arpa. IN      NS      abimanyu.e30.com.
	3                       IN      PTR     abimanyu.e30.com. ; Byte ke-4 IP Abimanyu
	' > /etc/bind/reverse/3.221.192.in-addr.arpa

	service bind9 restart
  ```

### Testing
Pada node client akan dilakukan test reverse domain.
> Script dijalankan pada **root node Nakula** dengan command `bash no5.sh`
- Nakula
  ```
  host -t PTR 192.221.3.3
  ```
![5 1](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/edd88e2c-8acf-4b9d-920a-32be7035434a)
![5 2](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/38d6024b-fe3e-410e-aa6e-39374ae3e394)

## **Soal Nomor 6**
Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.
## **Penyelesaian Soal Nomor 6**
Dilakukan konfigurasi `/etc/bind/named.conf.local` pada node Yudhistira dengan tambahan `also-notify dan allow-transfer`. Kemudian bind9 direstart menggunakan command `service bind9 restart`.
> Script dijalankan pada **root node Yudhistira** dengan command `bash no6.sh`
- Yudhistira
  ```
	echo -e '
	zone "abimanyu.e30.com" {
        	type master;
		notify yes;
		also-notify { 192.221.2.3; }; // IP Werkudara
		allow-transfer { 192.221.2.3; }; // IP Werkudara
        	file "/etc/bind/abimanyu.e30/abimanyu.e30.com";
	};
 	' > /etc/bind/named.conf.local

	service bind9 restart
  ```
Dilakukan juga konfigurasi `/etc/bind/named.conf.local` pada node Werkudara dengan tambahan zone. Kemudian bind9 direstart menggunakan command `service bind9 restart`.
> Script dijalankan pada **root node Werkudara** dengan command `bash no6.sh`
- Werkudara
  ```
	echo -e '
	zone "abimanyu.e30.com" {
    		type slave;
    		masters { 192.221.2.2; }; // IP Yudhistira
    		file "/var/lib/bind/abimanyu.e30/abimanyu.e30.com";
	};
 	' > /etc/bind/named.conf.local

	service bind9 restart
  ```

### Testing
Pada node Yudhistira service bind9 akan dimatikan.
- Yudhistira
  ```
  service bind9 stop
  ```
Kemudian dicek node client dengan melakukan test ping ke abimanyu.e30.com.
> Script dijalankan pada **root node Nakula** dengan command `bash no6.sh`
- Nakula
  ```
  ping abimanyu.e30.com -c 3
  ```
![6 1](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/7301c102-88e5-4e88-91fe-d24500a61f74)
![6 2](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/653787b2-beb4-460e-b16b-1306639ee8b1)
![6 3](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/dff49b19-99cf-40a5-ad84-f0d214e935c1)
![6 4](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/52efa2c7-a9b3-4667-985f-a0d0b9bf267d)

## **Soal Nomor 7**
Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu baratayuda.abimanyu.yyy.com dengan alias www.baratayuda.abimanyu.yyy.com yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.
## **Penyelesaian Soal Nomor 7**
Pada node Yudhistira dibuat direktori `/etc/bind/abimanyu.e30`. Kemudian dibuat file `abimanyu.e30.com` pada direktori tersebut dan file dilakukan konfigurasi dengan domain baratayuda.abimanyu.e30.com. Kemudian dilakukan konfigurasi juga pada `/etc/bind/named.conf.options` dengan menambahkan `allow-query{any;};`. Setelah selesai bind9 direstart menggunakan command `service bind9 restart`.
> Script dijalankan pada **root node Yudhistira** dengan command `bash no7.sh`
- Yudhistira
  ```
	echo -e '
	;
	; BIND data file for local loopback interface
	;
	$TTL    604800
	@       IN      SOA     abimanyu.e30.com. root.abimanyu.e30.com. (
	                      	      2     	; Serial
	                         604800         ; Refresh
	                          86400         ; Retry
	                        2419200         ; Expire
	                         604800 )       ; Negative Cache TTL
	;
	@		IN      NS      abimanyu.e30.com.
	@		IN      A       192.221.3.3		; IP Abimanyu 
	www		IN      CNAME   abimanyu.e30.com.	; Alias
	parikesit	IN      A       192.221.3.3		; IP Abimanyu
	www.parikesit	IN      CNAME   parikesit		; Alias
	ns1		IN	A	192.221.2.3		; IP Werkudara
	baratayuda	IN	NS	ns1
	www.baratayuda	IN      CNAME   baratayuda          	; Alias
	@       	IN      AAAA    ::1
	' > /etc/bind/abimanyu.e30/abimanyu.e30.com
	
	mkdir /etc/bind/abimanyu.e30
	
	echo -e '
	options {
	        directory "/var/cache/bind";
	
	        // If there is a firewall between you and nameservers you want
	        // to talk to, you may need to fix the firewall to allow multiple
	        // ports to talk.  See http://www.kb.cert.org/vuls/id/800113
	        // forwarders {
	        //      192.168.122.1;
	        // };
	
	        //========================================================================
	        // If BIND logs error messages about the root key being expired,
	        // you will need to update your keys.  See https://www.isc.org/bind-keys
	        //========================================================================
	        //dnssec-validation auto;
	        allow-query{any;};
	
	        auth-nxdomain no;    # conform to RFC1035
	        listen-on-v6 { any; };
	};
	' > /etc/bind/named.conf.options
	
	service bind9 restart
  ```
Dilakukan juga konfigurasi `/etc/bind/named.conf.local` pada node Werkudara dengan tambahan zone `baratayuda.abimanyu.e30.com`. Dibuat direktori `/etc/bind/baratayuda` lalu dibuat file `baratayuda.abimanyu.e30.com` pada direktori tersebut dan file dilakukan konfigurasi dengan domain baratayuda.abimanyu.e30.com. Kemudian dilakukan konfigurasi juga pada `/etc/bind/named.conf.options` dengan menambahkan `allow-query{any;};`. Setelah selesai bind9 direstart menggunakan command `service bind9 restart`. 
> Script dijalankan pada **root node Werkudara** dengan command `bash no7.sh`
- Werkudara
  ```
	echo -e '
	options {
	        directory "/var/cache/bind";
	
	        // If there is a firewall between you and nameservers you want
	        // to talk to, you may need to fix the firewall to allow multiple
	        // ports to talk.  See http://www.kb.cert.org/vuls/id/800113
	        // forwarders {
	        //      0.0.0.0;
	        // };
	
	        //========================================================================
	        // If BIND logs error messages about the root key being expired,
	        // you will need to update your keys.  See https://www.isc.org/bind-keys
	        //========================================================================
	        //dnssec-validation auto;
	        allow-query{any;};
	
	        auth-nxdomain no;    # conform to RFC1035
	        listen-on-v6 { any; };
	};
	' > /etc/bind/named.conf.options
	
	echo -e '
	zone "abimanyu.e30.com" {
	    type slave;
	    masters { 192.221.2.2; }; // IP Yudhistira
	    file "/var/lib/bind/abimanyu.e30/abimanyu.e30.com";
	};
	
	zone "baratayuda.abimanyu.e30.com" {
	        type master;
	        file "/etc/bind/baratayuda/baratayuda.abimanyu.e30.com";
	};
	' > /etc/bind/named.conf.local
	
	mkdir /etc/bind/baratayuda
	
	echo -e '
	;
	; BIND data file for local loopback interface
	;
	$TTL    604800
	@       IN      SOA     baratayuda.abimanyu.e30.com. root.baratayuda.abimanyu.e30.com. (
	                              2         ; Serial
	                         604800         ; Refresh
	                          86400         ; Retry
	                        2419200         ; Expire
	                         604800 )       ; Negative Cache TTL
	;
	@       	IN      NS          baratayuda.abimanyu.e30.com.
	@       	IN      A           192.221.3.3			; IP Abimanyu
	www       	IN      CNAME       baratayuda.abimanyu.e30.com.
	@               IN      AAAA	    ::1
	' > /etc/bind/baratayuda/baratayuda.abimanyu.e30.com
	
	service bind9 restart
  ```

### Testing
Pada node client dicek dengan melakukan test ping ke baratayuda.abimanyu.e30.com.
> Script dijalankan pada **root node Nakula** dengan command `bash no7.sh`
- Nakula
  ```
	ping abimanyu.e30.com -c 3
	ping baratayuda.abimanyu.e30.com -c 3
	ping www.baratayuda.abimanyu.e30.com -c 3
  ```
![7 1](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/41206985-6097-4ae0-b415-194c2aea4b2f)
![7 2](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/c10001d8-7c94-43de-9df2-a3d6f9cf0fbd)
![7 3](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/4fba27b7-1c5b-41f6-b7a7-3d50273b7e28)

## **Soal Nomor 8**
Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses rjp.baratayuda.abimanyu.yyy.com dengan alias www.rjp.baratayuda.abimanyu.yyy.com yang mengarah ke Abimanyu.
## **Penyelesaian Soal Nomor 8**
Pada node Werkudara dilakukan penambahan alias pada file `abimanyu.e30.com` dengan `www.rjp IN CNAME baratayuda.abimanyu.e30.com.`. Setelah selesai bind9 direstart menggunakan command `service bind9 restart`. 
> Script dijalankan pada **root node Werkudara** dengan command `bash no8.sh`
- Werkudara
  ```
	echo -e '
	$TTL    604800
	@       IN      SOA     baratayuda.abimanyu.e30.com. root.baratayuda.abimanyu.e30.com. (
	                              2         ; Serial
	                         604800         ; Refresh
	                          86400         ; Retry
	                        2419200         ; Expire
	                         604800 )       ; Negative Cache TTL
	;
	@               IN      NS		baratayuda.abimanyu.e30.com.
	@               IN      A		192.221.3.3			; IP Abimanyu
	www             IN      CNAME		baratayuda.abimanyu.e30.com.
	rjp		IN	A		192.221.3.3			; IP Abimanyu
	www.rjp		IN	CNAME		baratayuda.abimanyu.e30.com.
	' > /etc/bind/baratayuda/baratayuda.abimanyu.e30.com
	
	service bind9 restart
  ```

### Testing
Pada node client dicek dengan melakukan test ping ke rjp.baratayuda.abimanyu.e30.com.
> Script dijalankan pada **root node Nakula** dengan command `bash no8.sh`
- Nakula
  ```
	ping rjp.baratayuda.abimanyu.e30.com -c 3
	ping www.rjp.baratayuda.abimanyu.e30.com -c 3
  ```
![8 1](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/611ae895-2fde-4be5-8046-a3360b0975b7)
![8 2](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/f1dd3877-88f5-4ab5-8121-c93134e67642)

## **Soal Nomor 9 & 10**
Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker (yang juga menggunakan nginx sebagai webserver) yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker.
Kemudian gunakan algoritma Round Robin untuk Load Balancer pada Arjuna. Gunakan server_name pada soal nomor 1. Untuk melakukan pengecekan akses alamat web tersebut kemudian pastikan worker yang digunakan untuk menangani permintaan akan berganti ganti secara acak. Untuk webserver di masing-masing worker wajib berjalan di port 8001-8003. Contoh
    - Prabakusuma:8001
    - Abimanyu:8002
    - Wisanggeni:8003
## **Penyelesaian Soal Nomor 9 & 10**
Pada node Arjuna dilakukan konfigurasi load balancing. Kemudian dilakukan `symlink` untuk menghubungkan direktori `/etc/nginx/sites-available/jarkom` ke `/etc/nginx/sites-enabled/jarkom`. Setelah selesai bind9 direstart menggunakan command `service bind9 restart`. 
> Script dijalankan pada **root node Arjuna** dengan command `bash no9.sh`
- Arjuna
  ```
	echo '
	nameserver 192.221.2.2 # IP Yudhistira
	nameserver 192.221.2.3 # IP Werkudara
	nameserver 192.168.122.1 # IP Public
	' > /etc/resolv.conf
	
	echo 'upstream backend {
	  server 192.221.3.2; # IP PrabuKusuma
	  server 192.221.3.3; # IP Abimanyu
	  server 192.221.3.4; # IP Wisanggeni
	}
	
	server {
	  listen 80;
	  server_name arjuna.e30.com www.arjuna.e30.com;
	
	  location / {
	    proxy_pass http://backend;
	  }
	}
	' > /etc/nginx/sites-available/jarkom
	
	ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled/jarkom
	rm /etc/nginx/sites-enabled/default
	service nginx restart
  ```
Selanjutnya pada semua node web server dilakukan juga konfigurasi agar nama server `arjuna.e30.com` dapat terhubung. Dilakukan `symlink` untuk menghubungkan direktori lalu bind9 direstart menggunakan command `service bind9 restart`. 
> Script dijalankan pada **root semua node web server** dengan command `bash no9.sh`
- Semua node web server
  ```
	service php7.0-fpm start
	
	echo 'server {
	        listen 80;
	
	        root /var/www/jarkom;
	        index index.php index.html index.htm index.nginx-debian.html;
	
	        server_name _;
	
	        location / {
	                try_files $uri $uri/ /index.php?$query_string;
	        }
	
	        location ~ \.php$ {
	                include snippets/fastcgi-php.conf;
	                fastcgi_pass unix:/run/php/php7.0-fpm.sock;
	        }
	
	        location ~ /\.ht {
	                deny all;
	        }
	}' > /etc/nginx/sites-available/jarkom
	
	ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled/jarkom
	rm /etc/nginx/sites-enabled/default
	service nginx restart
  ```
Kemudian pada tiap node web server diberi file `index.php` berisi kalimat yang ingin ditampilkan.
- Semua node web server
	```
 	mkdir /var/www/jarkom
	```
- Wisanggeni
	```
 	echo "<?php echo 'Halo, Wisanggeni disini'; ?>" > /var/www/jarkom/index.php
 	```
- Abimanyu
	```
 	echo "<?php echo 'Halo, Abimanyu disini'; ?>" > /var/www/jarkom/index.php
 	```
- Prabukusuma
	```
 	echo "<?php echo 'Halo, Prabukusuma disini'; ?>" > /var/www/jarkom/index.php
 	```
 
### Testing
![9 1](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/b6359c19-1394-40c3-a622-0a0766172075)
![9 2](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/2daebb41-a830-4985-b784-57b2eb13a870)
![9 3](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/e310874d-f994-4987-a850-7dfdab8d70d3)
![9 6](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/c2952579-9dba-4511-acc6-d04c2105005c)
![9 9](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/26735ed7-df11-446d-916c-5ffcc555f9bc)

Pada node client dicek dengan melakukan lynx ke IP web server.
- Nakula
  ```
  lynx http://192.221.3.4
  ```
![9 4](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/5d0ad9ff-3bfa-4cbf-9f65-9aba600d6541)
![9 5](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/9f97693a-2eaf-4a8e-a787-b7a44ebf63e3)
  ```
  lynx http://192.221.3.3
  ```
![9 7](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/76c2a1fa-b9d2-4174-a89b-5f86f498c3a2)
![9 8](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/ffb39352-bffc-40a7-9b7c-84f70f7e5bbd)
  ```
  lynx http://192.221.3.2
  ```
![10 1](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/729f2aeb-21c5-4a03-a49b-b659d89aee18)
![10 2](https://github.com/hanamahes78/Jarkom-Modul-2-E30-2023/assets/108173681/cb8c112e-aa1f-4713-9d68-b74344903ab7)
