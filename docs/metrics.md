# Memahami metrik Cacti

## Yang sudah terlihat

| Metrik / status | Arti | Bukan bukti untuk |
|---|---|---|
| Processes | Jumlah proses pada host yang dipantau | Request website |
| Memory Usage | Memori sistem dan swap | Pengunjung |
| Logged in Users | Sesi user OS yang masuk | Pengunjung website |
| Load Average | Beban antrean proses sistem | Trafik HTTP |
| Device Up / response time | Hasil probe reachability dan waktu respons probe | Ketersediaan halaman end-to-end atau jumlah visitor |

Screenshot menunjukkan device `WEB` (`qyvv.site`) dan `Panel` (`panel.qyvv.site`) berstatus **Up**, masing-masing dengan 4 graph dan 5 data sources. Device response-time graph/status mengukur probe, bukan request riil dari pengguna.

## Kategori monitoring

1. **Availability:** apakah host/port merespons. TCP Ping ke 80/443 berguna untuk pemeriksaan dasar.
2. **Server metrics:** CPU, memory, process, load, dan interface counters dari SNMP atau template lokal.
3. **HTTP traffic:** jumlah request, response bytes, status code, atau endpoint tertentu dari access log atau web server metrics.
4. **Unique visitors:** estimasi berdasarkan client IP/cookie/session; bukan angka yang bisa diambil dari ping.

## Rencana web traffic (belum diverifikasi)

Sebelum menambahkan konfigurasi ke repo, buktikan semua berikut:

- Access log yang benar untuk virtual host `qyvv.site` dan format log-nya.
- Identitas user proses polling yang dapat membaca log dengan hak minimum.
- Script/input yang menghitung data per interval (mis. request count dan response bytes), dengan parsing format log yang sesuai.
- Data Input Method, Data Template, Graph Template, data source dan graph di Cacti.
- Grafik menerima sampel baru pada beberapa interval berurutan.

Jangan gunakan screenshot menu `Get Web Hits` saja sebagai bukti analytics aktif. Bukti harus menampilkan data source/graph yang terisi dengan nilai dan waktu terkini. Jika ada reverse proxy/Cloudflare, validasi apakah IP di log adalah IP visitor atau IP proxy sebelum menyebut hitungan visitor unik.
