# FP-SPJ-Wordpress-dan-Securitynya

Tugas yang di buat pada final project kali ini adalah wordpress dan keamanannya menggunakan SSL dan monitoring menggunakan Wazuh.
****
## langkah langkah install wordpress di ubuntu 22.04 LST Linux ##
1. Lakukan pembaruan Ubuntu 22.04
   Pertama-tama, jalankan perintah pembaruan sistem untuk memastikan semua paket di sistem kita mutakhir dan juga cache indeks paket APT dalam keadaan terbaru.
   
          *sudo apt update && sudo apt upgrade*
   
2. Instal Apache & PHP untuk WordPress
   Kita memerlukan web server Apache dan bahasa pemrograman PHP untuk setting CMS WordPress, mari kita install keduanya pada langkah ini.

          * sudo apt install apache2 *

   Setelah instalasi Apache selesai, aktifkan dan mulai layanannya.

          * sudo systemctl enable apache2 *

   Periksa status :
  
          * systemctl status apache2 *

   Catatan : alamat IP server dengan alamat Anda yang sebenarnya

          * http://192.168.56.111 *

   Instal PHP versi 8

   Versi default PHP tersedia untuk diinstal menggunakan repositori standar Ubuntu 22.04 LTS. Oleh karena itu, cukup jalankan perintah yang diberikan untuk menginstal PHP dan ekstensi yang diperlukan di sistem Anda.

         * sudo apt install -y php php-{common,mysql,xml,xmlrpc,curl,gd,imagick,cli,dev,imap,mbstring,opcache,soap,zip,intl} *

   cek versi php setelah di install

         * php -v *

 3. Instal MariaDB atau MySQL
    Kita bisa menggunakan MariaDB atau MySQL Database Server di Ubuntu 22.04 untuk menyimpan data yang dihasilkan oleh CMS WordPress. Di sini kami menggunakan Server MariaDB.

        * sudo apt install mariadb-server mariadb-client *

    Aktifkan, Mulai dan periksa status layanan:

        * sudo systemctl enable --now mariadb *

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

        * sudo apt install wget unzip *

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

6. Konfigurasikan Apache di Ubuntu 22.04
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

## langkah langkah pasang SSL dan key SSL ##
   Langkahnya adalah sebagai berikut :
1. Install mod_ssl
   
   ```$ sudo yum install mod_ssl```

   Setelah berhasil terinstall dilanjut membuat sertifikat baru

   ```$ sudo mkdir /etc/ssl/private```
   ```$ sudo chmod 700 /etc/ssl/private```
   ```$ sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/apache-selfsigned.key -out /etc/ssl/certs/apache-selfsigned.crt```   

Penting untuk memasukkan nama domain atau alamat IP publik server saat Anda dimintai Nama Umum (misalnya server FQDN atau nama kita). Nilai di sini harus sesuai dengan cara user mengakses server kita.

Keseluruhan petunjuknya akan terlihat seperti ini:

         Country Name (2 letter code) [XX]:US
State or Province Name (full name) []:Example
Locality Name (eg, city) [Default City]:Example 
Organization Name (eg, company) [Default Company Ltd]:Example Inc
Organizational Unit Name (eg, section) []:Example Dept
Common Name (eg, your name or your server's hostname) []:your_domain_or_ip
Email Address []:webmaster@example.com      










   

## langkah langkah pasang SSL dan key SSL ##
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


































       
