# Backup Database Script 1

Alternatif Backup Menggunakan Script. Bisa Dijalankan
[PerconaXtraBackup](https://docs.percona.com/percona-xtrabackup/8.0/)

## Install/Update Percona XtraBackup

    dnf update    dnf install xtrabackup

## Full Backup

1. Salin dan edit variabel yang diperlukan.
2. Simpan skrip dengan ekstensi `.sh`.
3. Jalankan skrip (gunakan CronJob untuk penjadwalan rutin).

```shell
#! /bin/sh

# Ubah variabel berikut sesuai kebutuhan
MYSQL_USER=admin
MYSQL_PASSWORD=#PASSWORD
MYSQL_HOST=localhost
MYSQL_PORT=3306
BASE_TARGET_DIR=#/root/simgosXtraBackup
# Penamaan File
WAKTU_STAMP=$(date "+%Y-%m-%d_%H-%M-%S")
LOG_FILE="$BASE_TARGET_DIR/simgosBackup.sh.log"

{

    echo --------RUNNING START AT ------- $WAKTU_STAMP -------------

    # Bersihkan target dir dan buat direktori baru
    cd $BASE_TARGET_DIR
    mkdir -p $BASE_TARGET_DIR/full-raw/$WAKTU_STAMP

    # Jalankan perintah xtrabackup
    xtrabackup --user=$MYSQL_USER --password=$MYSQL_PASSWORD --host=$MYSQL_HOST --port=$MYSQL_PORT --backup --compress --target-dir=$BASE_TARGET_DIR/full-raw/$WAKTU_STAMP

    # Buat zip untuk mempermudah sinkronisasi
    cd $BASE_TARGET_DIR
    zip -r "$WAKTU_STAMP.zip" full-raw/$WAKTU_STAMP
    rm -rf $BASE_TARGET_DIR/full-raw/$WAKTU_STAMP

    # Fungsi limitasi file dan hapus yang terlama
    sh ./file-cleaner.js

    echo #############ENDS########$(date "+%Y-%m-%d %H-%M-%S") --------

} 2>&1 | tee -a "$LOG_FILE"
```

## Gambaran Struktur Direktori di `BASE_TARGET_DIR`

```python
BASE_TARGET_DIR
├── full-raw
├── 2024-05-16_03-18-01.zip
├── 2024-05-17_03-18-01.zip
├── simgosBackup.sh.log
├── cleaner.js # OPSIONAL
└── backupSimgos.sh # OPSIONAL LOKASI SIMPAN
```

## Script hapus file lama

Script dibawah ini digunakan untuk menghapus file zip terlama dan menyisakan beberapa yang terbaru
Cocok bagi saya yang membuat full backup 2x/hari. Sesuaikan dengan kondisi anda

```shell
#!/bin/bash

# Direktori yang berisi file-file
DIR="{BASE_TARGET_DIR}"

# Jumlah file terbaru yang ingin disimpan
n=6

# Temukan dan hapus file tertua, simpan hanya n file terbaru
cd "$DIR" || { echo "Direktori tidak ditemukan"; exit 1; }

# Dapatkan daftar file yang diurutkan berdasarkan waktu modifikasi secara terbalik (terbaru di atas)
files=($(ls -1t *.zip))

# Dapatkan jumlah file di direktori
total_files=${#files[@]}

# Hitung jumlah file yang akan dihapus
files_to_delete=$((total_files - n))

if (( files_to_delete > 0 )); then
    # Loop melalui file dan hapus yang tertua
    for (( i = total_files - 1; i >= n; i-- )); do
        rm -f "${files[$i]}"
        echo "Dihapus: ${files[$i]}"
    done
else
    echo "Tidak ada yang perlu dihapus. Total file ($total_files) kurang dari atau sama dengan $n."
fi


# Assign the input filename to a variable
filename="$DIR/simgosBackup.sh.log"

# Check if the file exists
if [ ! -f "$filename" ]; then
  echo "File tidak ditemukan!"
  exit 1
fi

# Get file size in bytes
file_size=$(stat -c "%s" "$filename")

# Check if file size is greater than 10MB (10,000,000 bytes)
if [ "$file_size" -le 10000000 ]; then
  echo "File $filename kurang dari 10MB, tidak ada aksi yang dilakukan."
  exit 0
fi

# Get total line count of the file
total_lines=$(wc -l < "$filename")

# Calculate number of lines to delete (80%)
lines_to_delete=$(( $total_lines * 4 / 5 ))

# Delete top 80% of lines
sed -i "1,${lines_to_delete}d" "$filename"

# Print a success message
echo "Telah dihapus 80% baris teratas dari $filename"

filename="/root/simgosBackup.sh.cron.log"

# Check if the file exists
if [ ! -f "$filename" ]; then
  echo "File tidak ditemukan!"
  exit 1
fi

# Get file size in bytes
file_size=$(stat -c "%s" "$filename")

# Check if file size is greater than 10MB (10,000,000 bytes)
if [ "$file_size" -le 10000000 ]; then
  echo "File $filename kurang dari 10MB, tidak ada aksi yang dilakukan."
  exit 0
fi

# Get total line count of the file
total_lines=$(wc -l < "$filename")

# Calculate number of lines to delete (80%)
lines_to_delete=$(( $total_lines * 4 / 5 ))

# Delete top 80% of lines
sed -i "1,${lines_to_delete}d" "$filename"

# Print a success message
echo "Telah dihapus 80% baris teratas dari $filename"
```

## Contoh Cronjob Backup dan Sync menggunakan rclone

Cronjob untuk Backup DB setiap pukul 23.45 PM:
Cronjob untuk sinkronisasi setiap pukul 03.18 AM:

```bash
45 23 * * * sh {BASE_TARGET_DIR}p/simgosBackup.sh > /root/simgosBackup.sh.cron.log
18 3 * * * rclone sync --log-file="/root/cronB2.log" {BASE_TARGET_DIR} {NAMA_RCLONE_INSTANCE}:/{NAMA_BUCKET}
```
