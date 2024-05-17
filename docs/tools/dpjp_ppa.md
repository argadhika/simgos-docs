### DPJP to PPA

Script Ini Mengubah tulisan "DPJP" di tampilan SIMGOS Detail Kunjungan Menjadi "PPA", istilah yang sering dipakai di Faskes Tingkat Pertama
Tidak Mengubah tulisan di Laporan

```bash
filename=/var/www/html/production/webapps/application/SIMpel/classic/app.js

# Check if the file exists
if [ ! -f "$filename" ]; then
  echo "File not found!"
  exit 1
fi

# Use sed to replace the text
sed -i 's/DPJP\. "+s\.NAMA:""):"/PPA. "+s.NAMA:""):"/g' "$filename"

echo "Replacement complete."
```
