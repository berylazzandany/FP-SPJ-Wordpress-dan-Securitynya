# FP-SPJ-Wordpress-dan-Securitynya

Tugas yang di buat pada final project kali ini adalah wordpress dan keamanannya menggunakan SSL dan monitoring menggunakan Wazuh.
****
## langkah langkah install wordpress di Centos 7 ##
1. Lakukan pembaruan Centos 7.9
   Pertama-tama, jalankan perintah pembaruan sistem untuk memastikan semua paket di sistem kita mutakhir dan juga cache indeks paket APT dalam keadaan terbaru.
   
          *sudo yum update -y*
   
2. Instal Apache & PHP untuk WordPress
   Kita memerlukan web server Apache dan bahasa pemrograman PHP untuk setting CMS WordPress, mari kita install keduanya pada langkah ini.

          * sudo yum install apache -y*

   Setelah instalasi Apache selesai, aktifkan dan mulai layanannya.

          * sudo systemctl enable apache *

   Periksa status :
  
          * systemctl status apache *

   Catatan : alamat IP server dengan alamat Anda yang sebenarnya

          * http://192.168.56.111 *

   Instal PHP versi 8

   Versi default PHP tersedia untuk diinstal menggunakan repositori standar Ubuntu 22.04 LTS. Oleh karena itu, cukup jalankan perintah yang diberikan untuk menginstal PHP dan ekstensi yang diperlukan di sistem Anda.

         * yum install -y php php-{common,mysql,xml,xmlrpc,curl,gd,imagick,cli,dev,imap,mbstring,opcache,soap,zip,intl} *

   cek versi php setelah di install

         * php -v *

 3. Instal MariaDB atau MySQL
    Kita bisa menggunakan MariaDB atau MySQL Database Server di Ubuntu 22.04 untuk menyimpan data yang dihasilkan oleh CMS WordPress. Di sini kami menggunakan Server MariaDB.

        * yum install mariadb *

    Aktifkan, Mulai dan periksa status layanan:

        * sudo systemctl enable mariadb *

    Memeriksa:

        * systemctl status mariadb *
     Ctrl+C untuk keluar.

    Amankan Instalasi Basis Data Anda:

    Untuk mengamankan instance Database kami, jalankan perintah yang diberikan:

        * sudo mysql_secure_installation *

    Keluaran  

  Pertanyaan yang diberikan akan ditanyakan oleh sistem, contoh jawabannya juga diberikan di bawah ini:

    Enter current password for root (enter for none): Press ENTER
    Set root password? [Y/n]: Y
    New password: Set-your-new-password
    Re-enter new password: Set-your-new-password
    Remove anonymous users? [Y/n] Y
    Disallow root login remotely? [Y/n] Y
    Remove test database and access to it? [Y/n] Y
    Reload privilege tables now? [Y/n] Y

4. Buat Database untuk WordPress
   Masuk ke server Database Anda dengan menggunakan kata sandi yang telah Anda tetapkan untuk pengguna root.

        * sudo mysql -u root -p *

   Ikuti perintah untuk membuat DB baru. Namun, jangan lupa untuk mengganti new_user dengan nama apa pun yang ingin Anda berikan kepada pengguna Database Anda dan dengan cara yang sama - new_db dengan nama untuk Database dan kata sandi_Anda untuk kata sandinya.

     CREATE USER 'new_user'@'localhost' IDENTIFIED BY 'your_password';
        
     CREATE DATABASE new_db;
        
     GRANT ALL PRIVILEGES ON new_db.* TO 'new_user'@'localhost';
        
     FLUSH PRIVILEGES;
        
     Exit;

 5. Instal WordPress di Ubuntu 22.04
    File untuk mengatur WordPress perlu diunduh secara manual dan kita dapat melakukannya menggunakan terminal perintah. Berikut adalah perintah yang harus diikuti:

        * yum install wget unzip *

    Unduh WordPress:

        * wget https://wordpress.org/latest.zip *

    Ekstrak berkas:

        * sudo unzip latest.zip *

    Pindahkan ke folder web: 

        * sudo mv wordpress/ /var/www/html/ *

    Hapus file yang diunduh untuk mengosongkan ruang:

        * sudo rm latest.zip *

    Ubah izin file

        * sudo chown www-data:www-data -R /var/www/html/wordpress/ *

        * sudo chmod -R 755 /var/www/html/wordpress/ *

6. Konfigurasikan Apache di Centos 7.9
   Selanjutnya, aktifkan modul dan file konfigurasi Vhost server web Apache Anda untuk memastikan modul tersebut menyajikan file CMS PorcessWire tanpa kesalahan apa pun.

   Buat file konfigurasi untuk WordPress

       * sudo nano /etc/apache2/sites-available/wordpress.conf *

        <VirtualHost *:80>
        
        ServerAdmin admin@example.com
        
        DocumentRoot /var/www/html/wordpress
        ServerName example.com
        ServerAlias www.example.com
        
        <Directory /var/www/html/wordpress/>
        
        Options FollowSymLinks
        AllowOverride All
        Require all granted
        
        </Directory>
        
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
        
        </VirtualHost>

    Simpan file dengan menekan Ctlr+O , menekan tombol Enter , lalu keluar menggunakan Ctrl+X .

    Aktifkan host virtual

        * sudo a2ensite wordpress.conf *

    Aktifkan modul penulisan ulang

        * sudo a2enmod rewrite *

    Nonaktifkan halaman pengujian Apache default

        * sudo a2dissite 000-default.conf *

    Mulai ulang server web Apache untuk menerapkan perubahan:

        * sudo systemctl restart apache2 *

7. Pengaturan antarmuka web CMS WordPress

       http://192.168.56.111

## langkah langkah pasang SSL ##
   Langkahnya adalah sebagai berikut :
1. Install mod_ssl
   
```
   $ sudo yum install mod_ssl
```

   Setelah berhasil terinstall dilanjut membuat sertifikat baru

```
   $ sudo mkdir /etc/ssl/private
   $ sudo chmod 700 /etc/ssl/private
   $ sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/apache-selfsigned.key -out /etc/ssl/certs/apache-selfsigned.crt
```

Penting untuk memasukkan nama domain atau alamat IP publik server saat Anda dimintai Nama Umum (misalnya server FQDN atau nama kita). Nilai di sini harus sesuai dengan cara user mengakses server kita.

Keseluruhan petunjuknya akan terlihat seperti ini:

```
   Country Name (2 letter code) [XX]:US
   State or Province Name (full name) []:Example
   Locality Name (eg, city) [Default City]:Example 
   Organization Name (eg, company) [Default Company Ltd]:Example Inc
   Organizational Unit Name (eg, section) []:Example Dept
   Common Name (eg, your name or your server's hostname) []:your_domain_or_ip
   Email Address []:webmaster@example.com
```

Kedua file yang kita buat akan ditempatkan di subdirektori yang sesuai di direktori /etc/ssl.

Saat kita menggunakan OpenSSL, Anda juga harus membuat grup Diffie-Hellman yang kuat, yang digunakan dalam menegosiasikan Kerahasiaan Perfect Forward dengan klien. Dapat melakukannya dengan mengetik:

```
   $ sudo openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048
```

Kita sekarang memiliki semua komponen yang diperlukan dari antarmuka yang telah selesai. Hal berikutnya yang harus dilakukan adalah menyiapkan host virtual untuk menampilkan sertifikat baru.

Buka file baru di direktori /etc/httpd/conf.d:

   ```
   $ sudo vi /etc/httpd/conf.d/your_domain_or_ip.conf
   ```
Ketikkan perintah berikut / apabila menggunakan SSH di cmd bisa di paste :

```
   <VirtualHost *:443>
       ServerName your_domain_or_ip
       DocumentRoot /var/www/html
       SSLEngine on
       SSLCertificateFile /etc/ssl/certs/apache-selfsigned.crt
       SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key
   </VirtualHost>
```
2. Setting / atur parameter SSL
   Lanjutan dari setting your_domain_or_ip.conf
```
       . . .
   </VirtualHost>
   . . .
   
   # Begin copied text
   # from https://cipherli.st/
   
   SSLCipherSuite EECDH+AESGCM:EDH+AESGCM
   # Requires Apache 2.4.36 & OpenSSL 1.1.1
   SSLProtocol -all +TLSv1.2
   # SSLOpenSSLConfCmd Curves X25519:secp521r1:secp384r1:prime256v1
   # Older versions
   # SSLProtocol All -SSLv2 -SSLv3 -TLSv1 -TLSv1.1
   SSLHonorCipherOrder On
   # Disable preloading HSTS for now.  You can use the commented out header line that includes
   # the "preload" directive if you understand the implications.
   #Header always set Strict-Transport-Security "max-age=63072000; includeSubdomains; preload"
   Header always set Strict-Transport-Security "max-age=63072000; includeSubdomains"
   # Requires Apache >= 2.4
   SSLCompression off
   SSLUseStapling on
   SSLStaplingCache "shmcb:logs/stapling-cache(150000)"
   # Requires Apache >= 2.4.11
   # SSLSessionTickets Off
```

3. Membuat redirect dari HTTP ke HTTPS
   Untuk mengalihkan semua lalu lintas agar terenkripsi SSL, buat dan buka file yang diakhiri dengan .conf di direktori /etc/httpd/conf.d:

```
   $ sudo vi /etc/httpd/conf.d/non-ssl.conf

   <VirtualHost *:80>
          ServerName 192.168.x.x (contoh)
           Redirect "/" "https://192.168.x.x (contoh)"
   </VirtualHost>

```

4. Aplikasikan perubahan konfigurasi Apache :

```
   $ sudo apachectl configtest
```

Apabila outputnya :

```
. . .
Syntax OK
```

Mulai ulang service Apache dengan perintah :

```
   $ sudo systemctl restart httpd.service
```

Selanjutnya, pastikan port 80 dan 443 terbuka di firewall kita. Jika kita tidak menjalankan firewall, maka kita dapat melewatinya.

Jika Anda menjalankan firewall firewalld, kita dapat membuka port ini dengan mengetik :

```
   $ sudo firewall-cmd --add-service=http
   $ sudo firewall-cmd --add-service=https
   $ sudo firewall-cmd --runtime-to-permanent
```

Jika kita menjalankan iptablesfirewall, perintah yang perlu di jalankan sangat bergantung pada kumpulan aturan kita saat ini. Untuk kumpulan aturan dasar, dapat menambahkan akses HTTP dan HTTPS dengan mengetik:

```
   $ sudo iptables -I INPUT -p tcp -m tcp --dport 80 -j ACCEPT
   $ sudo iptables -I INPUT -p tcp -m tcp --dport 443 -j ACCEPT
```

5. Percobaan Enkripsi
   Sekarang siap untuk mencoba SSL server kita. Buka browser dan ketikkan ```https://domain_atau_ip```.

Karena sertifikat yang di buat tidak ditandatangani oleh salah satu otoritas sertifikat tepercaya di browser kita, kita mungkin akan melihat peringatan bahwa koneksi tidak private. Hal ini wajar dan normal. Kita hanya tertarik pada aspek enkripsi sertifikat, bukan validasi pihak ketiga atas keaslian host kita. Klik “ADVANCED” dan kemudian klik "Proceed to link / ip kita".

Jika kita melihat di bilah alamat browser, kita akan melihat beberapa indikasi keamanan parsial. Ini bisa berupa gembok dengan tanda “x” di atasnya atau segitiga dengan tanda seru. Dalam hal ini, ini berarti sertifikat tidak dapat divalidasi. Itu masih mengenkripsi koneksi Anda.

Jika Anda mengonfigurasi Apache untuk mengalihkan permintaan HTTP ke HTTPS, Anda juga dapat memeriksa apakah pengalihan berfungsi dengan benar di browser :

```
   https://domain_atau_ip
```

## langkah langkah pasang key SSH ##
   Langkahnya adalah sebagai berikut :
   1. Buat Kunci ssh di client machine

            ssh-keygen
      
      Secara default, ssh-keygenakan membuat pasangan kunci RSA 2048-bit, yang cukup aman untuk sebagian besar kasus          penggunaan (Anda dapat meneruskan tanda -b 4096untuk membuat kunci 4096-bit yang lebih besar).

      Setelah memasukkan perintah, Anda akan melihat prompt berikut:
      
            Output
         
            Generating public/private rsa key pair.
            Enter file in which to save the key (/your_home/.ssh/id_rsa):

      Tekan ENTERuntuk menyimpan pasangan kunci ke dalam .ssh/subdirektori di direktori home Anda, atau tentukan jalur       alternatif.

   Jika sebelumnya Anda telah membuat pasangan kunci SSH, Anda mungkin melihat perintah berikut:

      Output
         
      /home/your_home/.ssh/id_rsa already exists.
      Overwrite (y/n)?

   Jika Anda memilih untuk menimpa kunci pada disk, Anda tidak akan dapat lagi mengautentikasi menggunakan kunci       sebelumnya. Berhati-hatilah saat memilih yes, karena ini adalah proses destruktif yang tidak dapat dibatalkan.

Anda kemudian akan melihat prompt berikut:
         
      Output
         
      Enter passphrase (empty for no passphrase):

   Di sini Anda secara opsional dapat memasukkan frasa sandi yang aman, yang sangat disarankan. Frasa sandi menambahkan  lapisan keamanan tambahan untuk mencegah pengguna yang tidak sah masuk. Untuk mempelajari lebih lanjut tentang       keamanan, lihat tutorial kami tentang Cara Mengonfigurasi Otentikasi Berbasis Kunci SSH di Server Linux .

Anda kemudian akan melihat output berikut:
         
      Output
         
      Your identification has been saved in /your_home/.ssh/id_rsa.
      Your public key has been saved in /your_home/.ssh/id_rsa.pub.
      The key fingerprint is:
      a9:49:2e:2a:5e:33:3e:a9:de:4e:77:11:58:b6:90:26 username@remote_host
      The key's randomart image is:
      +--[ RSA 2048]----+
      |     ..o         |
      |   E o= .        |
      |    o. o         |
      |        ..       |
      |      ..S        |
      |     o o.        |
      |   =o.+.         |
      |. =++..          |
      |o=++.            |
      +-----------------+

Anda sekarang memiliki kunci publik dan pribadi yang dapat Anda gunakan untuk mengautentikasi. Langkah selanjutnya adalah menempatkan kunci publik di server Anda sehingga Anda dapat menggunakan otentikasi berbasis kunci SSH untuk login.

   2. Salin Kunci Publik
      
      Cara tercepat untuk menyalin kunci publik Anda ke host CentOS adalah dengan menggunakan utilitas bernama ssh-copy-id. Karena kesederhanaannya, metode ini sangat disarankan jika tersedia. Jika Anda tidak memiliki ssh-copy-idkunci yang tersedia di mesin klien Anda, Anda dapat menggunakan salah satu dari dua metode alternatif yang disediakan di bagian ini (menyalin melalui SSH berbasis kata sandi, atau menyalin kunci secara manual).

      Jika Anda tidak memiliki ssh-copy-idakses, tetapi Anda memiliki akses SSH berbasis kata sandi ke akun di server Anda, Anda dapat mengunggah kunci Anda menggunakan metode SSH konvensional.

Anda dapat melakukan ini dengan menggunakan catperintah untuk membaca konten kunci SSH publik di komputer lokal kami dan menyalurkannya melalui koneksi SSH ke server jarak jauh.

Di sisi lain, Anda dapat memastikan bahwa ~/.sshdirektori tersebut ada dan memiliki izin yang benar pada akun yang Anda gunakan.

Anda kemudian dapat menampilkan konten yang Anda salurkan ke dalam file yang dipanggil authorized_keysdalam direktori ini. Anda akan menggunakan >>simbol pengalihan untuk menambahkan konten alih-alih menimpanya. Ini memungkinkan Anda menambahkan kunci tanpa merusak kunci yang ditambahkan sebelumnya.

Perintah lengkapnya terlihat seperti ini:
        
    cat ~/.ssh/id_rsa.pub | ssh username@remote_host "mkdir -p ~/.ssh && touch ~/.ssh/authorized_keys && chmod -R go= ~/.ssh && cat >> ~/.ssh/authorized_keys"

Anda mungkin melihat pesan berikut:
   
      Output
         
      The authenticity of host '203.0.113.1 (203.0.113.1)' can't be established.
      ECDSA key fingerprint is fd:fd:d4:f9:77:fe:73:84:e1:55:00:ad:d6:6d:22:fe.
      Are you sure you want to continue connecting (yes/no)? yes

Ini berarti komputer lokal Anda tidak mengenali host jarak jauh. Ini akan terjadi saat pertama kali Anda terhubung ke host baru. Ketik yesdan tekan ENTERuntuk melanjutkan.

Setelah itu, Anda akan diminta memasukkan kata sandi akun pengguna jarak jauh:
    
      Output
         
      username@203.0.113.1's password:

Setelah memasukkan kata sandi Anda, konten id_rsa.pubkunci Anda akan disalin ke akhir file authorized_keysakun pengguna jarak jauh.

3. Autentikasi Server dengan Key SSH
Jika Anda berhasil menyelesaikan salah satu prosedur di atas, Anda seharusnya bisa masuk ke host jarak jauh tanpa kata sandi akun jarak jauh.

Proses dasarnya sama:
              
    ssh username@remote_host

Jika ini adalah pertama kalinya Anda terhubung ke host ini (jika Anda menggunakan metode terakhir di atas), Anda mungkin melihat sesuatu seperti ini:

      Output
         
      The authenticity of host '203.0.113.1 (203.0.113.1)' can't be established.
      ECDSA key fingerprint is fd:fd:d4:f9:77:fe:73:84:e1:55:00:ad:d6:6d:22:fe.
      Are you sure you want to continue connecting (yes/no)? yes

 4. Menonaktifkan Otentikasi Kata sandi di server
    Setelah Anda mengonfirmasi bahwa akun jarak jauh Anda memiliki hak administratif, masuklah ke server jarak jauh Anda dengan kunci SSH, baik sebagai root atau dengan akun yang memiliki sudohak istimewa. Kemudian, buka file konfigurasi daemon SSH:
              
          sudo vi /etc/ssh/sshd_config

Di dalam file, cari arahan bernama PasswordAuthentication. Ini mungkin bisa dikomentari. Jika ya, tekan iuntuk menyisipkan teks, lalu batalkan komentar pada baris tersebut dengan menghapus #di depan PasswordAuthenticationarahan. Saat Anda menemukan arahannya, atur nilainya menjadi no. Ini akan menonaktifkan kemampuan Anda untuk masuk melalui SSH menggunakan kata sandi akun:
/etc/ssh/sshd_config

      ...
      PasswordAuthentication no
      ...

Ketika Anda selesai melakukan perubahan, tekan ESClalu :wquntuk menulis perubahan pada file dan keluar. Untuk menerapkan perubahan ini, Anda perlu memulai ulang sshdlayanan.

    sudo systemctl restart sshd.service

Sebagai tindakan pencegahan, buka jendela terminal baru dan uji apakah layanan SSH berfungsi dengan benar sebelum menutup sesi ini:
              
    ssh username@remote_host

Setelah Anda memverifikasi layanan SSH, Anda dapat menutup semua sesi server saat ini dengan aman.
Daemon SSH di server CentOS Anda sekarang hanya merespons kunci SSH. Otentikasi berbasis kata sandi telah berhasil dinonaktifkan.  

## langkah langkah Install Wazuh ##
   Langkahnya adalah sebagai berikut :
  Memasang Wazuh
   1. Unduh dan jalankan asisten instalasi Wazuh
   
          curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh && sudo bash ./wazuh-install.sh -a

   Setelah asisten menyelesaikan instalasi, output menunjukkan kredensial akses dan pesan yang mengonfirmasi bahwa           instalasi berhasil.

          INFO: --- Summary ---
          INFO: You can access the web interface https://<wazuh-dashboard-ip>
              User: admin
              Password: <ADMIN_PASSWORD>
          INFO: Installation finished.
          
   sekarang telah menginstal dan mengkonfigurasi Wazuh.
    
   2. Akses antarmuka web Wazuh dengan https://<wazuh-dashboard-ip>dan kredensial Anda:

          Nama pengguna: admin
        
          Kata sandi: <ADMIN_PASSWORD>

   Catatan

Anda dapat menemukan kata sandi untuk semua pengindeks Wazuh dan pengguna API Wazuh di file di * wazuh-      passwords.txt* dalamnya *wazuh-install-files.tar*. Untuk mencetaknya, jalankan perintah berikut:
              
          sudo tar -O -xvf wazuh-install-files.tar wazuh-install-files/wazuh-passwords.txt
              
Jika Anda ingin menghapus instalasi komponen pusat Wazuh, jalankan asisten instalasi Wazuh menggunakan opsi -uatau 
–-uninstall.

   3. Tambahkan repositori Wazuh

      1. Impor kunci GPG:

              rpm --import https://packages.wazuh.com/key/GPG-KEY-WAZUH

      2. Tambahkan repositori:

              cat > /etc/yum.repos.d/wazuh.repo << EOF
              [wazuh]
              gpgcheck=1
              gpgkey=https://packages.wazuh.com/key/GPG-KEY-WAZUH
              enabled=1
              name=EL-\$releasever - Wazuh
              baseurl=https://packages.wazuh.com/4.x/yum/
              protect=1
              EOF

  Menyebarkan agen Wazuh

      Untuk menyebarkan agen Wazuh di titik akhir Anda, pilih manajer paket Anda dan edit variabel WAZUH_MANAGERuntuk memuat alamat IP atau nama host manajer Wazuh Anda.

              WAZUH_MANAGER="10.0.0.2" yum install wazuh-agent

Untuk opsi penerapan tambahan seperti nama agen, grup agen, dan kata sandi pendaftaran, lihat bagian Variabel penerapan untuk Linux .

Catatan:
Alternatifnya, jika Anda ingin menginstal agen tanpa mendaftarkannya, hilangkan variabel penerapan. Untuk mempelajari lebih lanjut tentang berbagai metode pendaftaran, lihat bagian pendaftaran agen Wazuh .

  Aktifkan dan mulai layanan agen Wazuh.

      systemctl daemon-reload

      systemctl enable wazuh-agent

      systemctl start wazuh-agent

Proses penerapan kini selesai, dan agen Wazuh berhasil dijalankan di sistem Linux Anda.

    Tindakan yang disarankan - Nonaktifkan pembaruan Wazuh

    Kompatibilitas antara agen Wazuh dan pengelola Wazuh dijamin bila versi pengelola Wazuh lebih baru atau sama dengan versi agen Wazuh. Oleh karena itu, kami menyarankan untuk menonaktifkan repositori Wazuh untuk mencegah peningkatan yang tidak disengaja. Untuk melakukannya, gunakan perintah berikut:

        sed -i "s/^enabled=1/enabled=0/" /etc/yum.repos.d/wazuh.repo

   Copot pemasangan agen Wazuh

Untuk menghapus instalasi agen, jalankan perintah berikut:

  Hapus instalasi agen Wazuh.

        yum remove wazuh-agent

Beberapa file ditandai sebagai file konfigurasi. Karena penunjukan ini, manajer paket tidak menghapus file-file ini dari sistem file. Jika Anda ingin menghapus semua file sepenuhnya, hapus foldernya /var/ossec.

   Nonaktifkan layanan agen Wazuh.

        systemctl disable wazuh-agent

        systemctl daemon-reload

Agen Wazuh sekarang telah dihapus sepenuhnya dari titik akhir Linux Anda.      


































       
