# Menyinkronkan Direktori Lokal ke S3 Compatible Object Storage Menggunakan Rclone

Dokumentasi ini memberikan panduan langkah demi langkah tentang cara menyinkronkan direktori lokal ke S3 Compatible Object Storage menggunakan Rclone, beserta skrip shell untuk otomatisasi.

## Prasyarat

1. **Install Rclone**: Pastikan Rclone sudah terinstall di sistem Anda. Jika belum, ikuti panduan instalasi untuk sistem operasi Anda dari [situs resmi Rclone](https://rclone.org/install/).

2. **Object Storage**: Anda perlu mempersiapkan endpoint URL, access key, dan secret key.

## Langkah-langkah

### Langkah 1: Konfigurasi Rclone untuk S3 Compatible Object Storage

1. Buka terminal.
2. Jalankan perintah berikut untuk memulai proses konfigurasi:

   ```sh
   rclone config
   ```

3. Ikuti petunjuknya:

   - Ketik `n` untuk membuat remote baru.
   - Beri nama remote (misalnya, `s3Storage`).
   - Pilih S3 Compatible
   - Masukkan endpoint URL, access key, dan secret key yang anda miliki
   - Atur konfigurasi lanjutan atau terima default dengan menekan Enter.
   - Konfirmasi konfigurasi dengan mengetik `y`.

### Langkah 2: Verifikasi Konfigurasi

Untuk memastikan konfigurasi sudah benar, lihat daftar bucket Anda:

```sh
rclone lsd s3Storage:
```

Anda harus melihat daftar bucket Anda.

### Langkah 3: Sinkronisasi Direktori Lokal ke Object Storage

Anda bisa menggunakan perintah berikut untuk menyinkronkan direktori lokal ke bucket Object Storage:

```sh
rclone sync /path/to/local/directory s3Storage:bucket-name
```

Ganti `/path/to/local/directory` dengan path direktori lokal Anda dan `bucket-name` dengan nama bucket Backblaze B2 Anda.

### Langkah 4: Otomatisasi dengan Skrip Shell

Buat skrip shell untuk mengotomatisasi proses sinkronisasi. Berikut adalah contoh skrip:

```sh
#!/bin/bash

# Konfigurasi
LOCAL_DIR="/path/to/local/directory"
REMOTE="s3Storage:bucket-name"
LOG_FILE="/path/to/logfile.log"

# Sinkronkan direktori lokal ke Object Storage
rclone sync $LOCAL_DIR $REMOTE --log-file=$LOG_FILE --log-level=INFO

# Cek status keluaran
if [ $? -eq 0 ]; then
    echo "Sinkronisasi berhasil pada $(date)" >> $LOG_FILE
else
    echo "Sinkronisasi mengalami kesalahan pada $(date)" >> $LOG_FILE
fi
```

Ganti `/path/to/local/directory`, `s3Storage:bucket-name`, dan `/path/to/logfile.log` dengan path dan nama bucket Anda.

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
