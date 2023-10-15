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
Menambahkan pada `root/.bashrc` masing-masing node.
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

## **Soal Nomor 3**
Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke abimanyu.yyy.com dan alias www.abimanyu.yyy.com.
## **Penyelesaian Soal Nomor 3**


## **Soal Nomor 4**
Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain parikesit.abimanyu.yyy.com yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.
## **Penyelesaian Soal Nomor 4**

## **Soal Nomor 5**
Buat juga reverse domain untuk domain utama. (Abimanyu saja yang direverse)
## **Penyelesaian Soal Nomor 5**

## **Soal Nomor 6**
Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.
## **Penyelesaian Soal Nomor 6**

## **Soal Nomor 7**
Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu baratayuda.abimanyu.yyy.com dengan alias www.baratayuda.abimanyu.yyy.com yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.
## **Penyelesaian Soal Nomor 7**

## **Soal Nomor 8**
Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses rjp.baratayuda.abimanyu.yyy.com dengan alias www.rjp.baratayuda.abimanyu.yyy.com yang mengarah ke Abimanyu.
## **Penyelesaian Soal Nomor 8**

## **Soal Nomor 9**
Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker (yang juga menggunakan nginx sebagai webserver) yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker.
## **Penyelesaian Soal Nomor 9**

## **Soal Nomor 10**
Kemudian gunakan algoritma Round Robin untuk Load Balancer pada Arjuna. Gunakan server_name pada soal nomor 1. Untuk melakukan pengecekan akses alamat web tersebut kemudian pastikan worker yang digunakan untuk menangani permintaan akan berganti ganti secara acak. Untuk webserver di masing-masing worker wajib berjalan di port 8001-8003. Contoh
    - Prabakusuma:8001
    - Abimanyu:8002
    - Wisanggeni:8003
## **Penyelesaian Soal Nomor 10**
