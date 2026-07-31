# SwiftWD — Setup Guide

Panduan setup awal dari HP kosong sampai panel siap dipakai. Contoh di bawah menggunakan IP, port, dan nama panel generik — sesuaikan dengan konfigurasi masing-masing.

## 1. Contoh Format Daftar Panel & Port

| Panel | Link Browser | Agent URL (WS) |
|---|---|---|
| Panel A | http://192.168.1.100:9000 | ws://192.168.1.100:8001 |
| Panel B | http://192.168.1.100:9001 | ws://192.168.1.100:8002 |
| Panel C | http://192.168.1.100:9002 | ws://192.168.1.100:8003 |

> Ganti IP, port, dan nama panel sesuai konfigurasi masing-masing device/server.

## 2. Persiapan HP (Android)

1. Buka **Settings → About Phone**, tap "Build Number" 7x untuk mengaktifkan **Developer Options**
2. Masuk ke **Developer Options**, aktifkan:
   - USB Debugging
   - (opsional) Stay Awake, biar layar gak mati saat dicolok
3. Colok HP ke PC via kabel USB
4. Di PC, cek koneksi:
   ```
   adb devices
   ```
   Pastikan device muncul dan statusnya `device` (bukan `unauthorized`). Kalau muncul prompt "Allow USB debugging?" di HP, tap **Allow**.

## 3. Aktifkan ADB over WiFi

1. Cari IP address HP (Settings → About Phone → Status → IP Address), misal `192.168.1.100`
2. Di PC (masih dengan kabel USB terpasang), jalankan:
   ```
   adb tcpip 5555
   adb connect 192.168.1.100:5555
   ```
3. Kalau muncul `connected to 192.168.1.100:5555`, kabel USB sudah bisa dicabut
4. Cek ulang koneksi WiFi:
   ```
   adb devices
   ```

## 4. Daftarkan Device ke Device Manager

1. Buka Device Manager (sistem multi-slot)
2. Pilih slot kosong, masukkan IP device dan beri nama sesuai panel (contoh: `Panel A`, `Panel B`, dst — samakan dengan nama panel di tabel port)
3. Simpan, pastikan status device menunjukkan **connected**

## 5. Buka Port di Firewall

Setiap panel butuh 2 port terbuka: **HTTP** (Link Browser) dan **WS** (Agent URL). Jalankan Command Prompt sebagai **Administrator**, lalu gunakan command berikut sesuai panel yang mau dibuka.

### Contoh satu panel

```
netsh advfirewall firewall add rule name="Panel A HTTP" dir=in action=allow protocol=TCP localport=9000
netsh advfirewall firewall add rule name="Panel A WS" dir=in action=allow protocol=TCP localport=8001
```

### Beberapa panel sekaligus (contoh batch script)

Simpan sebagai `open-ports.bat`, klik kanan → **Run as administrator**:

```bat
@echo off
netsh advfirewall firewall add rule name="Panel A HTTP" dir=in action=allow protocol=TCP localport=9000
netsh advfirewall firewall add rule name="Panel A WS" dir=in action=allow protocol=TCP localport=8001

netsh advfirewall firewall add rule name="Panel B HTTP" dir=in action=allow protocol=TCP localport=9001
netsh advfirewall firewall add rule name="Panel B WS" dir=in action=allow protocol=TCP localport=8002

netsh advfirewall firewall add rule name="Panel C HTTP" dir=in action=allow protocol=TCP localport=9002
netsh advfirewall firewall add rule name="Panel C WS" dir=in action=allow protocol=TCP localport=8003

echo Selesai. Semua port panel sudah dibuka.
pause
```

> Jalankan sekali saja per PC/server. Kalau nambah panel baru, tinggal tambahkan 2 baris `netsh` sesuai port panel baru tersebut.

## 6. Jalankan Agent & Launcher

1. Jalankan agent untuk panel yang device-nya sudah terhubung (langkah 4)
2. Jalankan launcher supaya proses tetap hidup 24 jam dan otomatis restart kalau agent terhenti atau device terputus
3. Cek dari **Link Browser** (kolom B di tabel) — pastikan dashboard panel terbuka
4. Cek dari **Agent URL / WS** (kolom C) — pastikan koneksi real-time (WebSocket) berhasil, biasanya ditandai status "connected" di dashboard

## 7. Troubleshooting Singkat

| Masalah | Kemungkinan Penyebab | Solusi |
|---|---|---|
| `adb devices` tidak menampilkan device | Device belum di-pair via USB / IP salah | Ulangi langkah 2–3 |
| Link Browser tidak bisa diakses | Port belum dibuka di firewall | Cek langkah 5, pastikan rule aktif |
| WS terputus-putus | Koneksi ADB over WiFi tidak stabil | Cek sinyal WiFi HP, pertimbangkan koneksi VPN yang lebih stabil |
| Device hilang dari Device Manager | HP restart / IP berubah (DHCP) | Set IP statis di HP, sambungkan ulang lewat langkah 3–4 |

## Kontak

Ada kendala setup atau butuh bantuan langsung?

**Telegram: [@SwiftWDwd](https://t.me/SwiftWDwd)**
