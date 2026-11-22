# Google AI Trial Tool — Penjelasan dalam Bahasa Indonesia

Catatan: Ini adalah ringkasan penjelasan dalam Bahasa Indonesia dari repo asli `duwqijlk/google-trial-tool`, dibuat supaya bisa dibaca tanpa harus mengerti Bahasa Cina. File skrip asli tidak disertakan di repo ini.

Sumber asli dibuat pada 19 November 2026. Isi aslinya disebut sebagai repost dari sebuah grup QQ.

## Apa itu?

Tool ini dibuat untuk mencari dan mengaktifkan masa trial gratis Google AI yang tersembunyi, yang tidak muncul di tombol upgrade biasa di aplikasi.

Caranya adalah dengan menyuntikkan kode ke proses aplikasi Google One yang berjalan di emulator, lalu memanggil API penagihan Google Play dari dalam aplikasi itu untuk menanyakan penawaran yang tersedia.

## Peringatan dari penulis aslinya

Penulis aslinya sendiri menulis di bagian atas README bahwa cara ini bisa membuat akun kena banned, dan meminta pembaca untuk hati-hati karena melibatkan pemanggilan tool.

## Yang dibutuhkan, secara umum

Menurut README aslinya, yang dibutuhkan antara lain:

- Emulator yang sudah berjalan, dengan akun Google sudah login dan cara bayar sudah terikat
- Alat baris perintah untuk terhubung ke emulator
- Frida di komputer dan Frida Server di perangkat
- Sebuah file APK tambahan yang berisi library penagihan
- Aplikasi Google One sudah terpasang di perangkat

Penulis menyarankan memakai Frida versi 16, bukan versi 17, karena ada perubahan di versi 17.

## Cara kerja, secara garis besar

1. Skrip Frida memuat library penagihan dari APK tambahan tersebut.
2. Skrip itu menanyakan beberapa produk langganan ke Google Play.
3. Untuk tiap produk, skrip memeriksa susunan harganya dan mencari yang fase pertamanya berharga nol, alias gratis. Kalau ada, itu dicatat sebagai trial.
4. Skrip memilih satu trial sesuai pilihan, standarnya yang paling lama.
5. Setelah itu, skrip memunculkan jendela pembayaran Google Play. Jendela itu tetap harus dikonfirmasi manual oleh pengguna.

Jadi tool ini tidak otomatis menyetujui pembayaran.

## Produk yang dibahas

README aslinya membahas empat pilihan utama:

- Pro, kapasitas 5TB, trial 3 bulan
- Pro varian lain, kapasitas 5TB juga, trial sampai 12 bulan. Ini yang direkomendasikan README aslinya.
- Ultra 5x, kapasitas 20TB, trial sampai 6 atau 12 bulan
- Ultra 20x, kapasitas 30TB, trial sampai 12 bulan

Ada juga beberapa produk lain yang dibahas, tapi produk-produk itu tidak punya trial.

Catatan penting di README per tanggal 19 November 2026: trial Ultra 5x dan 20x katanya sudah ditutup, penawarannya sudah dihapus. Jadi yang masih dibahas sebagai opsi utama adalah Pro.

Tombol upgrade biasa di aplikasi, menurut penulis, memakai ID produk yang tidak membawa trial.

## Kenapa cara lewat web disebut tidak bisa?

Menurut penulis, cara lewat web sudah dicoba dan tidak bisa melewati pemeriksaan server Google Play, karena:

- Token dan data pembayaran di web terikat ke sesi dan cepat kedaluwarsa.
- Kalau produk diubah di parameter halaman, tanda tangan keamanannya tidak cocok, sehingga server menolak.
- Cara lain lewat pengaya browser juga disebut sudah tidak tersedia, karena repo terkait sudah tidak ditemukan.

Kesimpulan penulis: trial tersembunyi itu tidak muncul di halaman web mana pun, sehingga satu-satunya cara yang dia anggap bisa adalah lewat suntikan di perangkat.

## Catatan penting lainnya dari penulis

- Setelah trial habis, langganan akan lanjut berbayar secara otomatis, jadi harus dibatalkan sebelum habis.
- Akun harus sudah terikat cara bayar dan belum punya langganan aktif, kalau tidak trial tidak bisa dipakai.
- Penulis menulis bahwa proses penagihan yang sudah dimulai bisa saja memakai kuota trial, walau jendela pembayarannya tidak sempat muncul.
- Penulis menyarankan memakai kartu dengan saldo kecil sebagai pengaman, dan melakukan semua ini di emulator.

## File di repo asli

Repo aslinya hanya punya tiga file:

- README, berisi penjelasan dan daftar produk.
- File shell, sebagai pengatur. Dia yang mencari emulator, memastikan layanan Frida berjalan, me-restart Google One supaya bersih, lalu menyuntikkan file Frida dan memantau hasilnya dari log.
- File Frida, sebagai otak. Dia yang memuat library penagihan, menanyakan produk, mencari trial gratis, memilih satu, lalu memunculkan jendela pembayaran.

Repo ini hanya berisi penjelasan di atas, bukan file-file tersebut.
