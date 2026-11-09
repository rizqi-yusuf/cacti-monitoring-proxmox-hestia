# Setup Cacti pada HestiaCP

## Konteks deployment

Panduan ini untuk instalasi yang telah dikonfirmasi:

| Item | Nilai |
|---|---|
| OS | Debian guest pada Proxmox CT 143 |
| Panel hosting | HestiaCP |
| Cacti web root | `/home/Qwyv/web/cacti.qyvv.site/public_html` |
| User poller | `Qwyv` |
| PHP CLI | `php8.5` |
| Polling | Setiap 5 menit |

Jangan gunakan path `/usr/share/cacti/site`, user `www-data`, atau cron paket Debian dalam panduan ini. Nilai tersebut berasal dari modul instalasi Debian generik dan tidak menggambarkan deployment HestiaCP ini.

## Prasyarat

- Akses root/sudo ke Debian guest yang menjalankan Cacti.
- Cacti sudah terpasang dan bisa dibuka melalui domainnya.
- Ekstensi PHP/SNMP dan paket yang dibutuhkan tersedia.
- Pastikan command menggunakan path dan versi PHP yang sama dengan deployment aktif.

## 1. Pasang dependensi yang diperlukan

Pada setup ini, perintah berikut dilaporkan berhasil dijalankan:

```bash
apt update
apt install php8.5-gmp php8.5-snmp snmpd snmp -y
```

Aktifkan agen SNMP hanya jika memang diperlukan untuk mengambil metrik lokal:

```bash
systemctl restart snmpd
systemctl enable snmpd
```

Jangan menyalin `community public` ke lingkungan terbuka. Atur akses SNMP terbatas sesuai kebutuhan dan firewall.

## 2. Import template Local Linux Machine

```bash
cd /home/Qwyv/web/cacti.qyvv.site/public_html/cli/
php8.5 import_package.php --filename=../install/templates/Local_Linux_Machine.xml.gz
```

Verifikasi lewat UI Cacti bahwa template tersedia dan dapat dikaitkan ke device yang benar. Import template saja belum berarti graph sudah dibuat.

## 3. Jalankan poller manual

```bash
sudo -u Qwyv php8.5 /home/Qwyv/web/cacti.qyvv.site/public_html/poller.php --force
```

Periksa hasil run dan log sebelum menyimpulkan berhasil. Poller aktif saja belum membuktikan graph tertentu menerima data.

## 4. Jadwalkan poller tiap lima menit

Konfigurasi yang dilaporkan berjalan di sistem ini:

```cron
*/5 * * * * Qwyv php8.5 /home/Qwyv/web/cacti.qyvv.site/public_html/poller.php > /dev/null 2>&1
```

Entri tersebut memakai format `/etc/crontab` (field user ada setelah field waktu). Pastikan tidak menambahkan jadwal duplikat. Setelah penyimpanan, periksa cron yang benar-benar aktif dan waktu **Last Run** Cacti.

## 5. Buat dan validasi graph

1. Tambahkan/konfirmasi device server dan hostname yang tepat.
2. Kaitkan device template yang sesuai untuk Linux.
3. Buat graph dari data query/template yang tersedia.
4. Jalankan poller dan tunggu beberapa interval.
5. Verifikasi graph menampilkan timestamp dan data baru, bukan hanya graph kosong.

Screenshot yang tersedia membuktikan graph Processes, Memory Usage, Logged in Users, dan Load Average serta device `WEB` dan `Panel` berstatus **Up**. Itu membuktikan monitoring server dasar/reachability, bukan jumlah pengunjung.

## Catatan pembeda dari instalasi Debian paket

PDF praktikum Debian 12 menggunakan instalasi `apt install cacti cacti-spine`, `/usr/share/cacti/site`, dan `www-data`. Itu jalur instalasi lain. Jangan gabungkan cron, permission, atau config file dari dua deployment berbeda. Temukan path aktif dari konfigurasi Hestia/Cacti sebelum mengubah permission atau scheduler.
