# FAQ Integrasi Satu Sehat

### Cara Cek Proses implementasi Satu Sehat

Masuk Ke link berikut, gunakan filter yang disediakan, untuk memudahkan pencarian faskes/daerah tujuan

[Link Data Monitoring Satu Sehat Seluruh Indonesia](https://satusehat.kemkes.go.id/data)

### Error Database result consisted of more than one row

Error ini memunculkan log error dan memberhentikan pendaftaranToEncounter

Solusi Tambahkan LIMIT 1; di fungsi getStatusPendaftaran dan getStatusHistori

Sumber:Telegram Simgos Operasional, 19 Feb 2024
