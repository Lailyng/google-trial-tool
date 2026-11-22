# Google AI Trial Tool

Isi ini adalah terjemahan, berasal dari grup QQ 797481450.
Kali ini bisa membuat akun kena banned, karena melibatkan pemanggilan tool. Hati-hati dalam memakai.

Melalui Frida yang disuntikkan ke proses Google One, tool ini memanggil API BillingClient untuk menemukan dan mengaktifkan trial gratis $0 yang tersembunyi.

## Prasyarat

| Komponen | Penjelasan |
| --- | --- |
| **Emulator MuMu** | Sudah berjalan, sudah login akun Google, sudah terikat cara bayar |
| **adb** | Sudah ada di PATH, atau diatur lewat variabel lingkungan `ADB_PATH` |
| **frida** (di komputer) | `pip install frida-tools`, versi 16 atau lebih baru |
| **frida-server** (di perangkat) | Sudah dikirim ke perangkat `/data/local/tmp/frida-server` dan sudah diberi izin eksekusi |
| **module.apk** (di perangkat) | APK yang berisi library BillingClient, sudah dikirim ke perangkat `/data/local/tmp/module.apk` |
| **Google One** | Sudah terpasang di perangkat (`com.google.android.apps.subscriptions.red`) |

## Persiapan Perangkat

```
# Hubungkan ke MuMu
adb connect 127.0.0.1:16384

# Kirim frida-server (unduh yang sesuai arsitektur dari https://github.com/frida/frida/releases)
adb push frida-server /data/local/tmp/frida-server
adb shell "chmod 755 /data/local/tmp/frida-server"

# Kirim module.apk (APK yang berisi com.android.billingclient)
adb push module.apk /data/local/tmp/module.apk
```

## Cara Pakai

```
# Tampilkan semua trial yang tersedia di akun saat ini
./auto-trial.sh --list

# Aktifkan Pro (5TB, $19.99/bulan) trial gratis 3 bulan
./auto-trial.sh --product pro --dur 3mo

# Aktifkan varian Pro (5TB, $19.99/bulan) trial gratis 12 bulan ★ Direkomendasikan
./auto-trial.sh --product pro12 --dur 12mo

# Aktifkan Ultra 20x (30TB, $199.99/bulan) trial gratis 12 bulan
./auto-trial.sh --product 20x --dur 12mo

# Aktifkan Ultra 5x (20TB, $99.99/bulan) trial gratis 6 bulan
./auto-trial.sh --product 5x --dur 6mo

# Otomatis memilih trial terpanjang (standarnya paket tertinggi)
./auto-trial.sh

# Tentukan port ADB MuMu
./auto-trial.sh --port 16416 --product pro --dur 3mo
```

### Parameter

| Parameter | Penjelasan | Nilai bawaan |
| --- | --- | --- |
| `--list` | Hanya menampilkan trial, tidak mengaktifkan | (mode aktif) |
| `--product` | `pro` / `pro12` / `5x` / `20x` / `auto` / ID lengkap `g1.*` | `auto` |
| `--dur` | `1mo` / `3mo` / `6mo` / `12mo` / `longest` | `longest` |
| `--port` | Port ADB MuMu | Dipindai otomatis |
| `--adb` | Jalur adb | Dideteksi otomatis |

### Variabel Lingkungan

| Variabel | Penjelasan |
| --- | --- |
| `ADB_PATH` | Jalur ke program adb yang bisa dieksekusi |
| `FRIDA_SCRIPT` | Jalur ke file frida-auto-trial.js (standarnya di folder yang sama) |
| `FRIDA_SERVER_PATH` | Jalur frida-server di perangkat (standarnya `/data/local/tmp/frida-server`) |
| `MODULE_APK_DEVICE` | Jalur module.apk di perangkat (standarnya `/data/local/tmp/module.apk`) |

## Perbandingan ID Produk

| Alias | ID Produk | Kapasitas | Biaya bulanan | Trial |
| --- | --- | --- | --- | --- |
| **pro12** ★ | `g1.2tb.ai.subs_variant` | 5TB | $19.99 | 6/12 bulan |
| **pro** | `g1.play.2tb.ai` | 5TB | $19.99 | 3 bulan |
| **5x** | `g1.play.20tb` | 20TB | $99.99 | 1/3/6/12 bulan |
| **20x** | `g1.play.ai.pro` | 30TB | $199.99 | 1/3/6/12 bulan |
| — | `g1.2tb.ai` | 5TB | $19.99 | Tidak ada |
| — | `g1.ai.pro` | 30TB | $199.99 | Tidak ada |
| — | `g1.30tb` | 30TB | $149.99 | Tidak ada |

Tombol "Upgrade" di dalam aplikasi memakai ID yang tidak membawa trial (`g1.2tb.ai` dan sejenisnya).

> **Catatan**: Per 2026-11-19, trial Ultra 5x/20x sudah ditutup (penawarannya sudah dihapus). Untuk Pro ada dua ID produk:
>
> - `g1.2tb.ai.subs_variant` (alias `pro12`): trial paling lama **12 bulan** gratis, direkomendasikan untuk dipakai lebih dulu
> - `g1.play.2tb.ai` (alias `pro`): trial 3 bulan gratis
>
> Catatan: Frida versi 17 ke atas ada perubahan besar (jembatan Java di versi 17 harus dimuat secara eksplisit), disarankan memakai Frida **16.7.19**.

## Cara Murni Lewat Web — Tidak Bisa

Menurut pengujian, cara lewat web berikut tidak bisa melewati pemeriksaan server Google Play:

1. **URL statis**: `rpctoken`, `_gfid`, dan blob pembayaran terikat ke sesi, begitu kedaluwarsa langsung tidak berlaku.
2. **Skrip console yang mengubah parameter ear**: halaman membuat ear (25 karakter), di `ear[13]` menyimpan produk dan di `ear[24]` menyimpan tanda tangan integritas. Tanda tangan itu terikat ke produk aslinya, jadi setelah produk diganti, server membalas 400, baik tanda tangannya dipertahankan maupun dihapus.
3. **Pengaya Chrome yang mencegat RPC**: cara komunitas di level payload RPC `Ngbm`, tapi repo terkait sudah tidak ditemukan (404).

> **Kesimpulan**: trial tersembunyi (`g1.play.2tb.ai`) tidak ditampilkan di halaman mana pun, dan web tidak bisa membuat sesi checkout yang sah untuk trial itu. Satu-satunya cara yang dianggap bisa adalah melalui Frida yang menyuntikkan API BillingClient di perangkat.

## Hal yang Perlu Diperhatikan

- Skrip hanya memunculkan jendela pembayaran Google Play, tetap harus dikonfirmasi manual (memasukkan kata sandi).
- Setelah trial habis, langganan lanjut berbayar otomatis, ingat untuk membatalkan lebih dulu.
- Akun harus sudah terikat cara bayar dan tidak punya langganan aktif, kalau tidak trial tidak tersedia.
- Hasil `launchBillingFlow` dengan kode 0 bisa saja memakai kuota trial, walau jendelanya tidak sempat tampil.
- Semua dilakukan di emulator, disarankan mengikat kartu dengan saldo kecil sebagai pengaman.

## Penjelasan File

- `auto-trial.sh` — Peluncur shell, mendeteksi perangkat secara otomatis, mengelola frida-server, menyuntikkan skrip, dan memantau hasilnya.
- `frida-auto-trial.js` — Skrip injeksi Frida, memuat BillingClient lewat DexClassLoader untuk menanyakan dan mengaktifkan trial.

Grup diskusi di akhir README aslinya: t.me/muihbb
