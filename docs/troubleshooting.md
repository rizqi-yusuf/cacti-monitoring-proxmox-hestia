# Troubleshooting Cacti

Mulai dari gejala dan bukti, ubah satu hal dalam satu waktu. Buat backup sebelum mengubah konfigurasi. Jangan jalankan `chown -R` pada seluruh direktori aplikasi.

## Graph kosong atau tidak diperbarui

1. Pastikan graph dan data source benar-benar terkait ke device.
2. Lihat waktu poller terakhir dan interval polling di Cacti.
3. Jalankan poller manual dengan user/path/PHP aktif deployment:

   ```bash
   sudo -u Qwyv php8.5 /home/Qwyv/web/cacti.qyvv.site/public_html/poller.php --force
   ```

4. Periksa output dan log Cacti untuk error spesifik.
5. Periksa hak akses pada path yang disebut error, termasuk permission setiap direktori parent/symlink. Ubah hak minimum saja.
6. Pastikan poller menerima host/data source. Angka `Hosts: 0` berarti run itu tidak memproses host; itu bukan bukti website down.

Jangan ikuti perintah generik `chown -R www-data ...` dari dokumen Debian pada instalasi HestiaCP ini.

## Device `Unknown` atau `Down`

- Pastikan hostname tepat dan resolvable dari guest yang menjalankan Cacti.
- Bedakan ICMP Ping dari TCP Ping. Gagal ICMP tidak otomatis berarti HTTPS mati.
- TCP Ping ke port 80/443 hanya membuktikan port menerima koneksi; tidak memeriksa isi halaman.
- Pastikan scheduler/poller berjalan dan cek log hasil probe.
- Jangan aktifkan SNMP untuk sekadar menguji port HTTP.

## Database connection error

- Periksa status MariaDB dan log error terlebih dahulu.
- Pastikan database host, nama DB, user, dan password di konfigurasi aktif cocok dengan DB sebenarnya.
- Jangan tempel password ke command line/history atau commit ke repo. Gunakan prompt interaktif untuk uji login.

## HTTP traffic tidak muncul

- Device status **Up** bukan request count.
- Pastikan input mengambil access log virtual host yang benar, memiliki hak baca, dan parsing format log sesuai.
- Cek bahwa data source terhubung ke graph dan poller mengambil data baru.
- Jika semua IP log terlihat sebagai IP proxy, jangan menyebut nilainya sebagai visitor unik sebelum konfigurasi real-client-IP tervalidasi.

## Log

Gunakan lokasi log Cacti yang dilaporkan konfigurasi aktif. Untuk web server, verifikasi domain dan path log Hestia yang sebenarnya sebelum membaca. Jangan mengandalkan lokasi Debian paket seperti `/var/log/apache2/error.log` jika deployment memakai konfigurasi/domain log berbeda.
