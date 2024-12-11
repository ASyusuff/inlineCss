# 🌐 Konfigurasi WordPress di Debian

![WordPress Logo](https://upload.wikimedia.org/wikipedia/commons/9/98/WordPress_blue_logo.svg)

Panduan ini menjelaskan langkah-langkah lengkap untuk menginstal dan mengonfigurasi WordPress pada server Debian. Ikuti langkah-langkah berikut untuk memastikan instalasi dan konfigurasi berjalan dengan lancar. 🚀

---

## 📋 Persiapan Awal

1. **Konfigurasi Jaringan**:
   - Gunakan IP statis sesuai kebutuhan.

2. **Update Repository dan Sistem**:
   - Jalankan perintah berikut:
     ```bash
     apt update && apt upgrade -y
     ```

3. **Instalasi Paket yang Dibutuhkan**:
   - Instal semua paket dalam satu perintah:
     ```bash
     apt install openssh-server bind9 dnsutils apache2 php php-mysql mariadb-server w3m unzip -y
     ```
   - Pastikan semua paket diinstal pada direktori `/etc`.

---

## ⚙️ Langkah Konfigurasi

### 1. Konfigurasi dan Pengujian SSH

- Verifikasi bahwa layanan SSH berjalan:
  ```bash
  systemctl status ssh
  ```
- Lakukan remote ke server melalui klien jika layanan sudah aktif.

### 2. Verifikasi dan Jalankan Apache2

- Opsional: Periksa versi Apache yang terinstal:
  ```bash
  apache2 -v
  ```
- Jalankan web server dan pastikan dapat diakses baik dari sisi server maupun klien.

### 3. Buat Halaman Web Utama

- Buat file `index.html` untuk domain utama:
  ```bash
  nano /var/www/html/index.html
  ```
- Tambahkan konten HTML sesuai kebutuhan.

### 4. Konfigurasi Virtual Host

- Buat dua Virtual Host untuk:
  - Domain utama (contoh: `nama.sch.id`).
  - Subdomain (contoh: `wordpress.nama.sch.id`).

- Tambahkan file konfigurasi untuk masing-masing di `/etc/apache2/sites-available/`, lalu aktifkan konfigurasi:
  ```bash
  a2ensite [nama-file-konfigurasi]
  a2dissite 000-default.conf
  systemctl restart apache2
  ```
- Tambahkan `ServerName` di file konfigurasi (opsional).

### 5. Konfigurasi DNS

- Edit file DNS di `/etc/bind/named.conf.default-zones` atau `/etc/bind/named.conf.local` untuk menambahkan domain utama dan subdomain.
- Salin file `db.local` dan `db.127`, lalu edit untuk konfigurasi forward dan reverse zone.
- Restart layanan Bind9:
  ```bash
  systemctl restart bind9
  ```
- Edit file resolver untuk memastikan konfigurasi DNS berfungsi dengan baik.

### 6. Instalasi WordPress

1. **Buat Directory untuk WordPress**:
   - Buat direktori untuk subdomain WordPress:
     ```bash
     mkdir /var/www/wordpress.nama.sch.id
     cd /var/www/wordpress.nama.sch.id
     ```

2. **Unduh dan Ekstrak WordPress**:
   ```bash
   wget https://wordpress.org/latest.zip
   unzip latest.zip
   ```

3. **Konfigurasi Database**:
   - Masuk ke MariaDB:
     ```bash
     mysql -u root -p
     ```
   - Buat database dan pengguna:
     ```sql
     CREATE DATABASE wordpress;
     CREATE USER 'tkj'@'localhost' IDENTIFIED BY 'tkj123';
     GRANT ALL PRIVILEGES ON wordpress.* TO 'tkj'@'localhost';
     FLUSH PRIVILEGES;
     ```
   - Keluar dari MariaDB.

4. **Konfigurasi File WordPress**:
   - Salin file konfigurasi contoh menjadi konfigurasi utama:
     ```bash
     cp wp-config-sample.php wp-config.php
     ```
   - Edit file `wp-config.php` untuk menambahkan detail berikut:
     ```php
     define('DB_NAME', 'wordpress');
     define('DB_USER', 'tkj');
     define('DB_PASSWORD', 'tkj123');
     ```
   - Simpan dan keluar.

5. Reload layanan web:
   ```bash
   systemctl reload apache2
   ```

### 7. Pengujian dan Konfigurasi WordPress

- Akses subdomain (`wordpress.nama.sch.id`) melalui browser.
- Ikuti langkah instalasi WordPress di halaman web.

---

## ✅ Pengujian

1. **Domain Utama**:
   - Pastikan `nama.sch.id` dapat diakses melalui server maupun klien.

2. **Subdomain WordPress**:
   - Pastikan `wordpress.nama.sch.id` dapat diakses dari server maupun klien.

---

## 📌 Catatan

- Jika ada kesalahan PHP, pastikan modul `php-mysql` terinstal:
  ```bash
  apt install php-mysql -y
  systemctl reload apache2
  ```

---

## 🎉 Penutup

Selamat belajar dan semoga sukses! Jika ada pertanyaan lebih lanjut, jangan ragu untuk menghubungi kami. 😊
