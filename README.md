# Jarkom-Modul-2-D09-2021

Nama Anggota | NRP
------------------- | --------------		
Dias Tri Kurniasari | 05111940000035
Nazhwa Ameera H | 05111940000133
Nur Moh. Ihsanuddien | 05111940000142

## Pengaturan Awal 

### Pengaturan Topologi
<img src="img/M02-0.1.png">

### Edit Konfigurasi Network

#### Foosha (Router)
``` 
    auto eth0
    iface eth0 inet dhcp
    auto eth1
    iface eth1 inet static
        address 192.196.1.1
        netmask 255.255.255.0
    auto eth2
    iface eth2 inet static
        address 192.196.2.1
        netmask 255.255.255.0
```

#### EniesLobby (DNS Master)
```
    auto eth0
    iface eth0 inet static
        address 192.196.2.2
        netmask 255.255.255.0
        gateway 192.196.2.1
```

#### Water7 (DNS Slave)
```
    auto eth0
    iface eth0 inet static
        address 192.196.2.3
        netmask 255.255.255.0
        gateway 192.196.2.1
```

#### Skypie (Web Server)
```
    auto eth0
    iface eth0 inet static
        address 192.196.2.4
        netmask 255.255.255.0
        gateway 192.196.2.1
```

#### Loguetown (Client)
```
    auto eth0
    iface eth0 inet static
        address 192.196.1.2
        netmask 255.255.255.0
        gateway 192.196.1.1
```

#### Alabasta (Client)
```
auto eth0
iface eth0 inet static
	address 192.196.1.3
	netmask 255.255.255.0
	gateway 192.196.1.1
```

## No 1
EniesLobby akan dijadikan sebagai DNS Master, Water7 akan dijadikan DNS Slave, dan Skypie akan digunakan sebagai Web Server. Terdapat 2 Client yaitu Loguetown, dan Alabasta. Semua node terhubung pada router Foosha, sehingga dapat mengakses internet.

1. Pertama, mengatur topologi dan edit network configuration sesuai dengan pengaturan diatas

2. Selanjutnya melakukan Restart pada semua node

3. Memasukkan `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.196.0.0/16` pada Foosha

4. Pada setiap node yang lain memasukkan `nameserver"192.196.2.2" > /etc/resolv.conf`

5. Kemudian melakukan pengecekan PING pada node, disini kami mengecek pada node LogueTown

![image](https://user-images.githubusercontent.com/65032157/139532689-4634d78b-fd8d-42b2-8bd3-3201838e5688.png)



## No 2
Membuat website utama dengan mengakses franky.yyy.com dengan alias www.franky.yyy.com pada folder kaizoku

Pada EniesLobby :
1. menginstall bind9 dengan command `apt-get update` dan `apt-get install bind9 -y`
2. Kemudian edit `/etc/bind/named.conf.local` dengan menambahkan :
```
    zone "franky.d09.com" {
            type master;
            file "/etc/bind/kaizoku/franky.d09.com";
    };
```
3. membuat folder kaizoku `mkdir /etc/bind/kaizoku`
4. melakukan copy `cp /etc/bind/db.local /etc/bind/kaizoku/franky.d09.com`
5. mengedit /etc/bind/kaizoku/franky.d09.com seperti berikut :
![image](https://user-images.githubusercontent.com/65032157/139532929-6e721beb-0aab-4eb3-90b2-762b85ced3fd.png)
6. restart bind9 dengan `service bind9 restart`

#### Testing
1. Pada `/etc/resolv.conf` dilakukan perubahan nameserver menuju IP dari EniesLobby `nameserver 192.196.2.2`
2. melakukan pengecekan Logue Town

![image](https://user-images.githubusercontent.com/65032157/139533043-31414fce-7104-4769-8905-1c214cb340ca.png)

3. pengecekan di Alabasta

![image](https://user-images.githubusercontent.com/65032157/139533071-77c0ce40-65d6-41f7-acbe-e232b5991a9c.png)


## No 3
Setelah itu buat subdomain super.franky.yyy.com dengan alias www.super.franky.yyy.com yang diatur DNS nya di EniesLobby dan mengarah ke Skypie.

#### EniesLobby
1. menambahkan seperti berikut pada `/etc/bind/named.conf.local`
```
zone "super.franky.d09.com" {
            type master;
            file "/etc/bind/kaizoku/super.franky.d09.com";
 };
```
2. melakukan copy file ke folder kaizoku `cp /etc/bind/db.local /etc/bind/kaizoku/super.franky.d09.com`
3. mengedit isi dari `/etc/bind/kaizoku/super.franky.d09.com` dengan konfigurasi sebagi berikut :
![image](https://user-images.githubusercontent.com/65032157/139533255-4780739f-898d-47fe-b8f9-816556921720.png)

4. Restart bind9 `service bind9 restart` 

#### Testing
1. Dilakukan pengecekan subdomain di LogueTown

![image](https://user-images.githubusercontent.com/65032157/139533328-65baa9ee-2e6a-44bf-aa0e-b37347cd0d13.png)

2. Pada alabasta

![image](https://user-images.githubusercontent.com/65032157/139533341-2163b51f-0779-4fe3-8a54-2af5a1c026ca.png)


## No 4
Buat juga reverse domain untuk domain utama

#### EniesLobby
1. menambahkan konfigurasi seperti berikut pada `/etc/bind/named.conf.local`
```
zone "2.196.192.in-addr.arpa" {
    type master;
    file "/etc/bind/kaizoku/2.196.192.in-addr.arpa";
};
```
2. melakukan copy `cp /etc/bind/db.local /etc/bind/kaizoku/2.196.192.in-addr.arpa`
3. melakukan edit pada `/etc/bind/kaizoku/2.196.192.in-addr.arpa` seperti berikut:

![image](https://user-images.githubusercontent.com/65032157/139533445-542444d5-1aac-4240-9882-7bd2c9eae1a3.png)
4. Restart bind9 `service bind9 restart `

##### LogueTown dan Alabasta
1. melakukan perintah pada masing-masing client
```
apt-get update
apt-get install dnsutils
```

2. Selanjutnya mengecek dengan melakukan `host -t PTR 192.196.2.2` pada alabasta

![image](https://user-images.githubusercontent.com/65032157/139533623-8505e937-6f7b-4230-a940-a4a98b619881.png)

3. pada Loguetown

![image](https://user-images.githubusercontent.com/65032157/139533639-de98ad49-287b-4183-a475-59c33b21593a.png)

## No 5
Supaya tetap bisa menghubungi Franky jika server EniesLobby rusak, maka buat Water7 sebagai DNS Slave untuk domain utama

#### EniesLobby
1. Melakukan edit pada `Edit file /etc/bind/named.conf.local`
```
zone "franky.d09.com" {
        type master;
        notify yes;
        also-notify { 192.196.2.3; }; // Masukan IP Water7 tanpa tanda petik
        allow-transfer { 192.196.2.3; }; // Masukan IP Water7 tanpa tanda petik
        file "/etc/bind/kaizoku/franky.d09.com";
};
```
2. Restart bind9 `service bind9 restart`

#### Water7
1. melakukan `apt-get update` dan `apt-get install bind9 -y`
2. setelah bind9 terinstall, dilakukan edit `pada /etc/bind/named.conf.local`:

```
zone "franky.d09.com" {
    type slave;
    masters { 192.196.2.2; };
    file "/var/lib/bind/franky.d09.com";
};
```

3. restart bind9 `service bind9 restart`

#### Testing

1. menambahkan pada `/etc/resolv.conf` dengan `nameserver 192.196.2.3 #IP Water7`
2. melakukan stop bind9 pada enieslobby

![image](https://user-images.githubusercontent.com/65032157/139534037-ddda5837-69fd-4d20-82ff-dcca9650d713.png)

3. pengecekan pada Alabasta
![image](https://user-images.githubusercontent.com/65032157/139534080-87f6158a-b9dc-4bf7-9d2c-b76ef580a2ed.png)


## No 6
Setelah itu terdapat subdomain mecha.franky.yyy.com dengan alias www.mecha.franky.yyy.com yang didelegasikan dari EniesLobby ke Water7 dengan IP menuju ke Skypie dalam folder sunnygo.

#### EniesLobby
1. pada `/etc/bind/kaizoku/franky.d09.com` ditambahkan:
```
ns1   IN      A       192.196.2.3 ;IP Water7
mecha   IN      A       ns1
```
2. Mengedit serta menambah code pada file `/etc/bind/named.conf.options` dengan :
```
//dnssec-validation auto;
allow-query{any;};
```
3.  Mengedit  pada `/etc/bind/named.conf.local`
```
zone "franky.d09.com" {
        type master;
        allow-transfer { 192.196.2.3; };
        file "/etc/bind/kaizoku/franky.d09.com";
};

zone "2.196.192.in-addr.arpa" {
    type master;
    file "/etc/bind/kaizoku/2.196.192.in-addr.arpa";
};
```
4. Restart bind9 `service bind9 restart`

#### Water7

1. Mengedit serta menambah code pada file `/etc/bind/named.conf.options` dengan :
```
//dnssec-validation auto;
allow-query{any;};
```
2. membuat folder sunnygo `mkdir /etc/bind/kaizoku`
3. melakukan copy `cp /etc/bind/db.local /etc/bind/sunnygo/mecha.franky.d09.com`
4. mengedit /etc/bind/sunnygo/mecha.franky.d09.comm seperti berikut :
![image](https://user-images.githubusercontent.com/65032157/139534352-618ec56f-dc78-4049-9553-ff8196a82085.png)

6. restart bind9 dengan `service bind9 restart`

#### Testing
1. alabasta
![image](https://user-images.githubusercontent.com/65032157/139534395-588897f4-ce82-4d4d-be54-b0b73d9e52af.png)

2. loguetown
 ![image](https://user-images.githubusercontent.com/65032157/139534418-10406867-8885-4763-a707-87176e343c86.png)


## No 7
Untuk memperlancar komunikasi Luffy dan rekannya, dibuatkan subdomain melalui Franky dengan nama general.mecha.franky.yyy.com dengan alias www.general.mecha.franky.yyy.com yang mengarah ke Skypie.

Pertama buka Water7, kemudian buka `/etc/bind/sunnygo/mecha.franky.d09.com` dan edit isinya seperti berikut.
```
    $TTL    604800
    @       IN      SOA     mecha.franky.d09.com. root.mecha.franky.d09.com. (
                                2         ; Serial
                            604800         ; Refresh
                            86400         ; Retry
                            2419200         ; Expire
                            604800 )       ; Negative Cache TTL
    ;
    @       IN      NS      mecha.franky.d07.com.
    @       IN      A       192.196.2.4 ;IP Skypie
    www     IN      CNAME   mecha.franky.d09.com.
    ns1     IN      A       192.196.2.4 ; IP Skypie
    general IN      NS      ns1
```
Setelah itu, menambahkan zone pada `/etc/bind/named.conf.local` sebagai berikut :
```
    zone "general.mecha.franky.d09.com" {
            type master;
            file "/etc/bind/sunnygo/general.mecha.franky.d09.com";
};
```
Lalu copy `/etc/bind/db.local` menjadi `/etc/bind/sunnygo/general.mecha.franky.d09.com`. Kemudian, lakukan konfigurasi file agar memiliki SOA `general.mecha.franky.d09.com.`, NS `general.mecha.franky.d09.com.`, record A yang mengarah ke `IP Skypie`, dan CNAME `www` pada `general.mecha.franky.d09.com.`.
Setelah itu, jalankan perintah `service bind9 restart` dan testing.

![image](https://user-images.githubusercontent.com/65032157/139535034-a074ac69-af96-4d01-9e03-b5683c359f29.png)



## No 8
Setelah melakukan konfigurasi server, maka dilakukan konfigurasi Webserver. Pertama dengan webserver www.franky.yyy.com. Pertama, luffy membutuhkan webserver dengan DocumentRoot pada /var/www/franky.yyy.com.

Pertama buka EnniesLoby, kemudian buka `/ect/bind/kaizoku/franky.d09.com` dan edit isinya seperti berikut.
```
    $TTL    604800
    @       IN      SOA     franky.d09.com. root.franky.d09.com. (
                                2         ; Serial
                            604800         ; Refresh
                            86400         ; Retry
                            2419200         ; Expire
                            604800 )       ; Negative Cache TTL
    ;
    @       IN      NS      franky.d09.com.
    @       IN      A       192.196.2.4 ; IP Skypie 
    ns2     IN      A       192.196.2.3 ; IP Water7
    mecha   IN      NS      ns2
```
Kemudian jalankan perintah `service bind9 restart`.

Selanjutnya, buka logueTown untuk install `lynx`.

Lalu, buka Skypie. Pertama install `apache2`, `php`, dan `libapache2-mod-php7.0`. Kemudian melakukan `wget` untuk mendownload file yang diperlukan. Setelah itu lakukan unzip.
```
    apt-get install wget
    wget https://github.com/FeinardSlim/Praktikum-Modul-2-Jarkom/archive/refs/heads/main.zip
    apt-get install unzip
    unzip main.zip
    cd Praktikum-Modul-2-Jarkom-main
    unzip franky.zip
    unzip super.franky.zip
    unzip general.mecha.franky.zip
```
Setelah itu, pindah ke `/etc/apache2/sites-available`.Lalu, copy file `000-default.conf` menjadi file `franky.d09.com.conf`. Kemudian buka file tersebut dan tambahkan sebagai berikut :
```
    ServerAdmin webmaster@localhost
    ServerName franky.d09.com
    ServerAlias www.franky.d09.com
    DocumentRoot /var/www/franky.d09.com
```
Kemudian buat directory baru dengan nama `franky.d09.com` pada `/var/www/` menggunakan command `mkdir /var/www/franky.d09.com`. Setelah itu, copy isi dari folder `franky` yang telah didownload ke `/var/www/franky.d09.com`.
```
    cp /root/Praktikum-Modul-2-Jarkom-main/franky/index.php /var/www/franky.d09.com
    cp /root/Praktikum-Modul-2-Jarkom-main/franky/home.html /var/www/franky.d09.com
```
Kemudian jalankan perintah `a2ensite franky.d09.com ` dan `service apache2 restart`.

<img src="img/M02-8.png">


## No 9
Setelah itu, Luffy juga membutuhkan agar url www.franky.yyy.com/index.php/home dapat menjadi menjadi www.franky.yyy.com/home.

Pertama buka Skypie, kemudian jalankan perintah `a2enmod rewrite` dan `service apache2 restart`.
Setelah itu pindah ke `/var/www/franky.d09.com` dan buat file `.htaccess` dengan isi sebagai berikut :
```
    RewriteEngine On
    RewriteRule ^home$ index.php/home
```
Setelah itu, buka file `/etc/apache2/sites-available/franky.d09.com.conf` dan tambahkan seperti berikut :
```
    <Directory /var/www/franky.d09.com>
        Options +FollowSymLinks -Multiviews
        AllowOverride All
    </Directory>
```
Setelah itu, jalankan perintah `service bind9 restart` dan testing.

<img src="img/M02-9.png">


## No 10
Setelah itu, pada subdomain www.super.franky.yyy.com, Luffy membutuhkan penyimpanan aset yang memiliki DocumentRoot pada /var/www/super.franky.yyy.com

Pertama buka Skypie, kemudian pindah ke `/etc/apache2/sites-available`. Lalu copy file `000-default.conf` menjadi file `super.franky.d09.com.conf`. Setelah itu, buka file tersebut dan tambahkan isinya sebagai berikut :
```
    ServerAdmin webmaster@localhost
    #DocumentRoot /var/www/html
    ServerName super.franky.d09.com
    ServerAlias www.super.franky.d09.com
    DocumentRoot /var/www/super.franky.d09.com
```
Kemudian buat directory baru dengan nama `super.franky.d09.com` pada `/var/www/` menggunakan command `mkdir /var/www/super.franky.d09.com`. Setelah itu, copy isi dari folder `super.franky` yang telah didownload ke `/var/www/super.franky.d09.com`.
```
    cp -r /root/Praktikum-Modul-2-Jarkom-main/super.franky/error /var/www/super.franky.d09.com
    cp -r /root/Praktikum-Modul-2-Jarkom-main/super.franky/public /var/www/super.franky.d09.com
```
Kemudian jalankan perintah `a2ensite super.franky.d09.com ` dan `service apache2 restart`.

<img src="img/M02-10.png">


## No 11
Akan tetapi, pada folder /public, Luffy ingin hanya dapat melakukan directory listing saja.
n
Pertama buka Skypie, kemudian pindah ke `/etc/apache2/sites-available` lalu buka file `super.franky.d09.com.conf` dan tambahkan isinya sebagai berikut :
```
    <Directory /var/www/super.franky.d09.com/public>
        Options +Indexes
    </Directory>
```
Setelah itu, jalankan perintah `service bind9 restart` dan testing.

<img src="img/M02-11.png">


## No 12
Tidak hanya itu, Luffy juga menyiapkan error file 404.html pada folder /errors untuk mengganti error kode pada apache.

Pertama buka Skypie, kemudian pindah ke `/etc/apache2/sites-available` lalu buka file `super.franky.d09.com.conf` dan tambahkan isinya dengan `ErrorDocument 404 /error/404.html`.

<img src="img/M02-12.png">


## No 13
Luffy juga meminta Nami untuk dibuatkan konfigurasi virtual host. Virtual host ini bertujuan untuk dapat mengakses file asset www.super.franky.yyy.com/public/js menjadi www.super.franky.yyy.com/js.
Pertama buka Skypie, kemudian pindah ke `/etc/apache2/sites-available` lalu buka file `super.franky.d09.com.conf` dan tambahkan isinya dengan `Alias "/js" "/var/www/super.franky.d09.com/public/js"`

<img src="img/M02-13.png">


## Error dan Kendala
- Kesalahan dalam pembuatan script (kami membuat setiap soal beda script tidak jadi satu) sehingga konfigurasinya berubah mengikuti perintah yang baru bukan menggabungkan dari awal.

<img src="img/M02-E1.jpg">

<img src="img/M02-E2.jpg">

- Terkadang lupa menjalankan script yang seharusnya dijalankan terlebih dahulu sebelum menajalankan script untuk menyelesaikan soal.
- Kami mengalami kendala dalam penyelesaian soal nomor 14-17 sehingga soal-soal tersebut tidak kami kerjakan.
- Pada pengerjaan pertama kami tidak menyimpan konfigurasi untuk tiap soal dalam script sehingga kami harus mengetik ulang konfigurasinya jika ingin mengerjakan lagi.
