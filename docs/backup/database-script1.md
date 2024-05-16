# Backup Database Script 1

Alternatif Backup Menggunakan Script. Bisa Dijalankan
[PerconaXtraBackup](https://docs.percona.com/percona-xtrabackup/8.0/)

## Install/Update Percona XtraBackup

    dnf update    dnf install xtrabackup

## Melakukan Full Backup

1. Copy dan edit variabel yang diperlukan
2. Simpan dengan ekstensi .sh
3. Jalankan (Gunakan CronJob untuk penjadwalan rutin)

```shell
#! /bin/sh

# Ubah variabel berikut sesuai kebutuhan
MYSQL_USER=admin
MYSQL_PASSWORD=#PASSWORD
MYSQL_HOST=localhost
MYSQL_PORT=3306
BASE_TARGET_DIR=#/root/simgosXtraBackup
LOG_FILE="$BASE_TARGET_DIR/simgosBackup.sh.log"
{
# Penamaan File
WAKTU_STAMP=$(date "+%Y-%m-%d_%H-%M-%S")
echo --------RUNNING START AT ------- $WAKTU_STAMP -------------
cd $BASE_TARGET_DIR
# bersihkan target dir
mkdir $BASE_TARGET_DIR/full-raw
rm -rf $BASE_TARGET_DIR/full-raw/$WAKTU_STAMP
mkdir $BASE_TARGET_DIR/full-raw/$WAKTU_STAMP

# command xtrabackup
xtrabackup --user=$MYSQL_USER --password=$MYSQL_PASSWORD --host=$MYSQL_HOST --port=$MYSQL_PORT --backup --compress --target-dir=$BASE_TARGET_DIR/full-raw/$WAKTU_STAMP
cd $BASE_TARGET_DIR


#buat zip untuk mempermudah sinkronisasi
zip -r "$WAKTU_STAMP.zip" full-raw/$WAKTU_STAMP
rm -rf $BASE_TARGET_DIR/full-raw/$WAKTU_STAMP
cd $BASE_TARGET_DIR

# fungsi limitasi file dan hapus yang terlama
#node cleaner.js

echo #############ENDS########$(date "+%Y-%m-%d %H-%M-%S") --------

} 2>&1 | tee -a "$LOG_FILE"

```

Gambaran File di folder BASE_TARGET_DIR

```
-BASE_TARGET_DIR
--/full-raw
--2024-05-16_03-18-01.zip
--2024-05-17_03-18-01.zip
--simgosBackup.sh.log
--cleaner.js #OPSIONAL
--backupSimgos.sh #OPSIONAL LOKASI SIMPAN
```

## Contoh Sinkronisasi ke tempat lain (rclone-backblazeB2)

Cronjob setiap pukul 03.18 AM sinkronsi

```bash
18 3 * * * rclone sync --log-file="/root/cronB2.log" {BASE_TARGET_DIR} {NAMA_RCLONE_INSTANCE}:/{NAMA_BUCKET}
```
