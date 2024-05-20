# Fungsi SQL Fix 'div' tag dan New Line di Report

### Muncul Tulisan DIV di Hasil Report

Munculnya tag `div` di report dan kemungkinan tag 'html' lain karena setting markup teksfield jasperreport tidak menggunakan 'html' melainkan 'none'.

Cara mengatasinya yaitu menambahkan `markup="html"` pada texfiled yang diinginkan\
(teksfield bisa lihat di Fungsi Mysql/ lewat JasperSoft Studio)\
(CONTOH pada file CetakCatatanMedik.jrxml)

```xml
<textElement textAlignment="Justified" verticalAlignment="Top" >
```

MENJADI

```xml
<textElement textAlignment="Justified" verticalAlignment="Top" markup="html">
```

### Tidak Ada NewLine / Baris Baru

Tidak adanya baris baru dikarenakan jasperreport tidak mengenal tag 'div' meskipun markup sudah diset di html. JasperReport hanya mengenal beberapa tag html dasar. Sehingga perlu adanya fungsi replace tag ini menjadi tag `br`. Tag newline yang dikenal JasperReport.

Alternatif 1 mengubah tag tersebut di tingkat fungsi database

```sql
USE `master`;
-- Dumping structure for function master.parseHTML
DELIMITER //
CREATE FUNCTION `parseHTML`(
	`PHTML` TEXT
) RETURNS text CHARSET utf8mb4
    READS SQL DATA
    DETERMINISTIC
BEGIN
   DECLARE KETERANGAN_JASPER TEXT;
   SELECT TRIM(BOTH '<div>' FROM KETERANGAN_JASPER) INTO KETERANGAN_JASPER;
   SELECT TRIM(BOTH '</div>' FROM KETERANGAN_JASPER) INTO KETERANGAN_JASPER;
   SELECT REPLACE(PHTML,'<div><br></div>','<br>') INTO KETERANGAN_JASPER;
   SELECT REPLACE(KETERANGAN_JASPER,'<div>','<br><div>') INTO KETERANGAN_JASPER;
   RETURN KETERANGAN_JASPER;
END//
DELIMITER ;
```

Alternatif 2 mengubah tag tersebut di tingkat JasperReport File \
(CONTOH pada file CetakCatatanMedik.jrxml)

```js
![CDATA[$F{CATATAN}]]
```

MENJADI

```js
![CDATA[$F{CATATAN}.replaceAll("(?i)<div>", "\n").replaceAll("(?i)</div>", "").replaceAll("(?i)<br>", "")]]
```

SELENGKAPNYA JIKA KEDUANYA DILAKUKAN \(MARKUP HTML DAN REPLACE DIV)\
(CONTOH pada file CetakCatatanMedik.jrxml)

```xml
            <textField textAdjust="StretchHeight">
                <reportElement x="0" y="0" width="181" height="23" uuid="e4e72430-d54d-4ac7-9a4f-fece8c4c97c5"/>
                <box topPadding="2" leftPadding="2" bottomPadding="2" rightPadding="2"/>
                <textElement textAlignment="Justified" markup="html">
                    <font fontName="Arial"/>
                </textElement>
                <textFieldExpression><![CDATA[$F{CATATAN}.replaceAll("(?i)<div>", "\n").replaceAll("(?i)</div>", "").replaceAll("(?i)<br>", "")]]></textFieldExpression>
            </textField>

```
