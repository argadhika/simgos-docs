# Backup

## Poin Penting Backup

Pastikan anda menempatkan data di minimal 3 tempat terpisah, contoh :

1. (onsite) Server Utama itu sendiri
2. (onsite) komputer / server lain di ruangan/gedung terpisah (terjangkau secara fisik)
3. (offsite) Online Cloud Storage / VPS /Hosting

## Rekomendasi Alat/Tools/Software Backup

1. rsync (software copy paste advance untuk linux, bisa untuk sync)
2. rclone ( software untuk mounting dan sinkronisasi file lokal dan cloud )

## Rekomendasi Lokasi Offsite Backup

Bisa Menggunakan rclone untuk sync ke cloud storage atau server lain

writer merekomendasikan menggunakan Object Cloud Storage S3 Compatible karena protokol yang cukup umum dan banyak digunakan

Selain itu, menaruh data pada third party yang memiliki datacenter membuat kemungkinan data hilang/rusak akibat force majeur sangat kecil (jika dibanding menyimpan di server sendiri beda lokasi)

S3 Compatible Cloud Storage Indonesia

1. [BiznetGIO](https://www.biznetgio.com/product/neo-object-storage)
2. [NevaCloud](https://nevacloud.com/object-storage/)
3. [CloudRaya](https://cloudraya.com/id/storage-raya-cdn/)
4. [IndonesiaCloud](https://indonesiancloud.com/object-storage-service/)
5. [IDCloudHost](https://idcloudhost.com/object-storage/)

S3 Compatible Cloud Storage Punya Server di Indonesia

1. AWS S3
2. Google Cloud
3. Alibaba Cloud

S3 Compatible Cloud Storage Lainnya

1. BackBlaze B2
