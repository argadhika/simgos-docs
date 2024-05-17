# FAQ tentang Laporan

### Halaman "an error occured during excecution, please try again later"

Kemungkinan pertama karena service tomcat tidak berjalan, yang dikarenakan java version terupdate (setelah melakukan dnf update)

1. Pastikan service tomcat berjalan\
   `systemctl status tomcat`
2. Restart ulang jika Service Mati\
   `systemctl restart tomcat`
3. Apabila gagal, silahkan cek file \
   `nano /etc/systemd/system/tomcat.service'`\
   Pastikan versi JAVA `rpm -qa java-11-openjdk` dan lokasi benar
4. Sesuaikan dengan versi dan direktori java terinstall.
5. Restart Service\
   `systemctl daemon-relad`
   `systemctl restart tomcat`
