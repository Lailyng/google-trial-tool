# Google AI Trial Tool

> Terjemahan Bahasa Indonesia dari README asli. Susunan bagian mengikuti README aslinya.
>
> Catatan: Ini hanya terjemahan penjelasan untuk dibaca. File skrip asli tidak disertakan di repo ini, dan baris perintah persis dari aslinya tidak disalin di sini.

Isi aslinya disebut sebagai repost, dari grup QQ 797481450.
Kali ini bisa membuat akun kena banned, karena melibatkan pemanggilan tool. Hati-hati dalam memakai.

Melalui Frida yang disuntikkan ke proses Google One, tool ini memanggil API BillingClient untuk menemukan dan mengaktifkan trial gratis $0 yang tersembunyi.

## Prasyarat

| Komponen | Penjelasan |
| --- | --- |
| Emulator MuMu | Sudah berjalan, sudah login akun Google, sudah terikat cara bayar |
| adb | Sudah ada di PATH, atau diatur lewat variabel lingkungan |
| frida (di komputer) | Sudah terpasang, versi 16 atau lebih baru |
| frida-server (di perangkat) | Sudah dikirim ke perangkat dan sudah diberi izin eksekusi |
| module.apk (di perangkat) | APK yang berisi library BillingClient, sudah dikirim ke perangkat |
| Google One | Sudah terpasang di perangkat |

## Persiapan Perangkat

Persiapan perangkat, secara garis besar:

1. Hubungkan ke emulator MuMu.
2. Kirim file frida-server ke perangkat, lalu beri izin eksekusi.
3. Kirim file module.apk ke perangkat.

File frida-server diunduh dari halaman rilis Frida, pilih yang sesuai arsitektur perangkat.

## Cara Pakai

Cara pakai yang dijelaskan di README aslinya, dalam bentuk ringkasan:

- Hanya melihat daftar semua trial yang tersedia di akun saat ini, tanpa mengaktifkan apa pun.
- Mengaktifkan Pro (5TB, $19.99 per bulan) dengan trial gratis 3 bulan.
- Mengaktifkan varian Pro (5TB, $19.99 per bulan) dengan trial gratis 12 bulan. Ini yang direkomendasikan, ditandai bintang di aslinya.
- Mengaktifkan Ultra 20x (30TB, $199.99 per bulan) dengan trial gratis 12 bulan.
- Mengaktifkan Ultra 5x (20TB, $99.99 per bulan) dengan trial gratis 6 bulan.
- Otomatis memilih trial terpanjang (standarnya paket tertinggi), tanpa menyebut produk dan durasi secara spesifik.
- Menentukan port ADB MuMu secara manual, lalu mengaktifkan Pro dengan trial 3 bulan.

### Parameter

| Parameter | Penjelasan | Nilai bawaan |
| --- | --- | --- |
| Daftar saja | Hanya menampilkan trial, tidak mengaktifkan | Mode aktif |
| Produk | Pilihan produk: Pro, Pro varian 12 bulan, Ultra 5x, Ultra 20x, otomatis, atau ID produk lengkap | Otomatis |
| Durasi | Pilihan durasi trial: 1 bulan, 3 bulan, 6 bulan, 12 bulan, atau yang terpanjang | Yang terpanjang |
| Port | Port ADB MuMu | Dipindai otomatis |
| Jalur adb | Jalur ke program adb | Dideteksi otomatis |

### Variabel Lingkungan

| Variabel | Penjelasan |
| --- | --- |
| Jalur adb | Jalur ke program adb yang bisa dieksekusi |
| Jalur skrip Frida | Jalur ke file skrip Frida, standarnya di folder yang sama |
| Jalur frida-server di perangkat | Jalur frida-server di perangkat, standarnya di folder sementara perangkat |
| Jalur module.apk di perangkat | Jalur module.apk di perangkat, standarnya di folder sementara perangkat |

## Perbandingan ID Produk

| Alias | Kapasitas | Biaya bulanan | Trial |
| --- | --- | --- | --- |
| pro12 (direkomendasikan) | 5TB | $19.99 | 6 / 12 bulan |
| pro | 5TB | $19.99 | 3 bulan |
| 5x | 20TB | $99.99 | 1 / 3 / 6 / 12 bulan |
| 20x | 30TB | $199.99 | 1 / 3 / 6 / 12 bulan |
| — | 5TB | $19.99 | Tidak ada |
| — | 30TB | $199.99 | Tidak ada |
| — | 30TB | $149.99 | Tidak ada |

Tombol "Upgrade" di dalam aplikasi memakai ID produk yang tidak membawa trial.

> Catatan, per 19 November 2026, trial Ultra 5x / 20x sudah ditutup, penawarannya sudah dihapus. Untuk Pro ada dua ID produk:
>
> - Varian Pro 12 bulan: trial paling lama 12 bulan gratis, direkomendasikan untuk dipakai lebih dulu
> - Pro biasa: trial 3 bulan gratis
>
> Catatan: Frida versi 17 ke atas ada perubahan besar, jembatan Java di versi 17 harus dimuat secara eksplisit. Disarankan memakai Frida 16.7.19.

## Cara Murni Lewat Web — Tidak Bisa

Menurut pengujian penulis, cara lewat web berikut tidak bisa melewati pemeriksaan server Google Play:

1. **URL statis**: token, ID, dan data pembayaran terikat ke sesi, begitu kedaluwarsa langsung tidak berlaku.
2. **Skrip console yang mengubah parameter ear**: halaman membuat ear sepanjang 25 karakter, satu bagian menyimpan produk dan bagian lain menyimpan tanda tangan integritas. Tanda tangan itu terikat ke produk aslinya, jadi setelah produk diganti, server membalas dengan kesalahan 400, baik tanda tangannya dipertahankan maupun dihapus.
3. **Pengaya Chrome yang mencegat RPC**: cara komunitas di level payload RPC, tapi repo terkait sudah tidak ditemukan (404).

> Kesimpulan: trial tersembunyi tidak ditampilkan di halaman mana pun, dan web tidak bisa membuat sesi checkout yang sah untuk trial itu. Satu-satunya cara yang dianggap bisa oleh penulis adalah melalui Frida yang menyuntikkan API BillingClient di perangkat.

## Hal yang Perlu Diperhatikan

- Skrip hanya memunculkan jendela pembayaran Google Play, tetap harus dikonfirmasi manual, termasuk memasukkan kata sandi.
- Setelah trial habis, langganan lanjut berbayar otomatis, ingat untuk membatalkan lebih dulu.
- Akun harus sudah terikat cara bayar dan tidak punya langganan aktif, kalau tidak trial tidak tersedia.
- Hasil pemanggilan alur penagihan yang berhasil bisa saja memakai kuota trial, walau jendelanya tidak sempat tampil.
- Semua dilakukan di emulator, disarankan mengikat kartu dengan saldo kecil sebagai pengaman.

## Penjelasan File

- `auto-trial.sh` — Peluncur shell, mendeteksi perangkat secara otomatis, mengelola frida-server, menyuntikkan skrip, dan memantau hasilnya.
- `frida-auto-trial.js` — Skrip injeksi Frida, memuat BillingClient lewat DexClassLoader untuk menanyakan dan mengaktifkan trial.

Grup diskusi di akhir README aslinya: t.me/muihbb
