# Menyinkronkan Direktori Lokal ke Backblaze Menggunakan Rclone

Dokumentasi ini memberikan panduan langkah demi langkah tentang cara menyinkronkan direktori lokal ke Backblaze B2 Cloud Storage menggunakan Rclone, beserta skrip shell untuk otomatisasi.

## Prasyarat

1. **Install Rclone**: Pastikan Rclone sudah terinstall di sistem Anda. Jika belum, ikuti panduan instalasi untuk sistem operasi Anda dari [situs resmi Rclone](https://rclone.org/install/).

2. **Akun Backblaze B2**: Anda memerlukan akun Backblaze B2. Jika belum punya, daftar di [Backblaze B2](https://www.backblaze.com/b2/cloud-storage.html).

## Langkah-langkah

### Langkah 1: Daftar ke Backblaze dan Buat Bucket

1. **Daftar ke Backblaze**:

   - Kunjungi situs [Backblaze B2](https://www.backblaze.com/b2/cloud-storage.html) dan daftar untuk akun baru.
   - Ikuti proses pendaftaran hingga selesai dan verifikasi email Anda.

2. **Masuk ke Akun Backblaze**:

   - Setelah pendaftaran, login ke akun Backblaze Anda.

3. **Buat Bucket**:

   - Di dashboard Backblaze B2, navigasi ke "Buckets".
   - Klik "Create a Bucket".
   - Beri nama bucket Anda dan pilih jenis bucket (Public atau Private).
   - Klik "Create a Bucket" untuk menyelesaikan pembuatan bucket.

4. **Buat Application Key**:
   - Di dashboard, navigasi ke "App Keys".
   - Klik "Add a New Application Key".
   - Beri nama untuk key Anda dan tetapkan izin yang sesuai.
   - Klik "Create New Key" dan salin `Application Key ID` dan `Application Key`. Anda hanya akan melihat key ini sekali, jadi pastikan untuk menyimpannya dengan aman.

### Langkah 2: Konfigurasi Rclone untuk Backblaze B2

1. Buka terminal.
2. Jalankan perintah berikut untuk memulai proses konfigurasi:

   ```sh
   rclone config
   ```

3. Ikuti petunjuknya:

   - Ketik `n` untuk membuat remote baru.
   - Beri nama remote (misalnya, `mybackblaze`).
   - Input `b2` untuk Backblaze B2.
   - Masukkan `Account ID` dan `Application Key` yang Anda buat sebelumnya.
   - Pilih endpoint default.
   - Atur konfigurasi lanjutan atau terima default dengan menekan Enter.
   - Konfirmasi konfigurasi dengan mengetik `y`.

### Langkah 3: Verifikasi Konfigurasi

Untuk memastikan konfigurasi sudah benar, lihat daftar bucket Backblaze B2 Anda:

```sh
rclone lsd mybackblaze:
```

Anda harus melihat daftar bucket Anda.

### Langkah 4: Sinkronisasi Direktori Lokal ke Backblaze

Anda bisa menggunakan perintah berikut untuk menyinkronkan direktori lokal ke bucket Backblaze B2:

```sh
rclone sync /path/to/local/directory mybackblaze:bucket-name
```

Ganti `/path/to/local/directory` dengan path direktori lokal Anda dan `bucket-name` dengan nama bucket Backblaze B2 Anda.

### Langkah 5: Otomatisasi dengan Skrip Shell

Buat skrip shell untuk mengotomatisasi proses sinkronisasi. Berikut adalah contoh skrip:

```sh
#!/bin/bash

# Konfigurasi
LOCAL_DIR="/path/to/local/directory"
REMOTE="mybackblaze:bucket-name"
LOG_FILE="/path/to/logfile.log"

# Sinkronkan direktori lokal ke Backblaze B2
rclone sync $LOCAL_DIR $REMOTE --log-file=$LOG_FILE --log-level=INFO

# Cek status keluaran
if [ $? -eq 0 ]; then
    echo "Sinkronisasi berhasil pada $(date)" >> $LOG_FILE
else
    echo "Sinkronisasi mengalami kesalahan pada $(date)" >> $LOG_FILE
fi
```

Ganti `/path/to/local/directory`, `mybackblaze:bucket-name`, dan `/path/to/logfile.log` dengan path dan nama bucket Anda.

### Langkah 6: Jadwalkan Skrip

Untuk menjalankan skrip pada interval waktu tertentu, gunakan `cron`:

1. Buka file crontab untuk diedit:

   ```sh
   crontab -e
   ```

2. Tambahkan cron job. Misalnya, untuk menjalankan skrip setiap hari jam 2 pagi:

   ```sh
   0 2 * * * /path/to/your/script.sh
   ```

   Ganti `/path/to/your/script.sh` dengan path ke skrip Anda.

### Tips Tambahan

- **Dry Run**: Uji sinkronisasi dengan dry run untuk memastikan berfungsi seperti yang diharapkan:

  ```sh
  rclone sync /path/to/local/directory mybackblaze:bucket-name --dry-run
  ```

- **Output Terperinci**: Untuk output lebih detail, tambahkan flag `-v`:

  ```sh
  rclone sync /path/to/local/directory mybackblaze:bucket-name -v
  ```

- **Pembatasan Bandwidth**: Untuk membatasi penggunaan bandwidth, gunakan flag `--bwlimit`:

  ```sh
  rclone sync /path/to/local/directory mybackblaze:bucket-name --bwlimit 1M
  ```

Dengan mengikuti langkah-langkah ini, Anda dapat menyinkronkan direktori lokal ke Backblaze B2 menggunakan Rclone dengan efektif, memastikan data Anda tersimpan dengan aman di cloud.
