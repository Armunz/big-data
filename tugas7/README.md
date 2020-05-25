# Analisis Time Series (Kebutuhan Listrik di Irlandia) menggunakan Spark dan KNIME

Berikut adalah workflow KNIME-nya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/workflow.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/workflow%20%282%29.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/workflow%20%283%29.JPG?raw=true)

## 1. Business Understanding
Menganalisis Rata-rata Penggunaan Listrik di Irlandia pada kurun waktu tertentu (time series), yaitu:

 1. **Total Usage**: Penggunaan Listrik Total.
 2. **Usage by Year**: Penggunaan Listrik per Tahun.
 3. **Usage by Month**: Penggunaan Listrik per Bulan.
 4. **Usage by Week**: Penggunaan Listrik per Minggu.
 5. **Usage by Day of Week**: Penggunaan Listrik berdasarkan hari dalam Seminggu.
 6. **Usage by Day**: Penggunaan Listrik per Hari.
 7. **Usage by Day Segment**: Penggunaan Listrik per Hari pada periode jam tertentu.
 8. **Usage by Day Classifier**: Penggunaan Listrik pada saat Weekend dan Weekday.
 9. **Usage by Hour**: Penggunaan Listrik pada jam tertentu.

## 2. Data Understanding

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/data%20preparation.JPG?raw=true)

Dataset-nya terdiri dari 3 kolom yaitu:

 1. **meterID**: id meteran listrik.
 2. **enc_datetime**: tanggal yang telah terenkripsi.
 3. **reading**: meteran yang terbaca pada meteran listrik.

## 3. Data Preparation
Dataset yang digunakan adalah dataset meteran listrik di Irlandia. Data diload menggunakan node **File Reader**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/data%20preparation%282%29.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/data%20preparation.JPG?raw=true)

Lalu data akan di-ubah formatnya menjadi DataFrame/RDD agar dapat dilakukan query menggunakan Spark.

Karena prosesnya menggunakan Spark, maka kita perlu menggunakan node **Create Local Big Data Environment**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/environment.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/modeling.JPG?raw=true)


![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/modeling%282%29.JPG?raw=true)

Dataset yang sebelumnya sudah diload ke **File Reader**, akan diload ke HIVE menggunakan metanode **Load Data**.

Di dalam metanode Load Data, terdapat 2 proses, yaitu **DB Table Creator** dan **DB Loader**. Pada node **DB Table Creator**, table HIVE akan diberi nama **meter**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/db%20table%20creator%20config.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/db%20table%20creator%20input%20output.JPG?raw=true)

Node **DB Table Creator** memerlukan 2 input, yaitu: 

 - **kotak merah** (DB Connection dimana disini menggunakan Spark HIVE)
 -  **segitiga garis hitam** (berasal dari File Reader)

Sedangkan untuk output-nya adalah kotak merah yang menandakan DB Connection yang telah berhasil dibuat.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/db%20loader%20config.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/db%20loader%20input%20output.JPG?raw=true)

Lalu pada node **DB Loader**, memerlukan 3 input yaitu:

 - **segitiga hitam** (berasal dari File Reader).
 - **kotak merah** (DB Connection dari DB Table Creator).
 - **kotak garis biru** (Koneksi HIVE dari node Create Local Big Data Environment).

dan 1 output, yaitu:

 - **kotak coklat** (data yang telah berbentuk HIVE).

Ketika di-execute, maka hasilnya akan seperti berikut.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/db%20loader%20hive%20output.JPG?raw=true)

Jika ingin mengeceknya HIVE-nya menggunakan DBeaver, maka hasilnya seperti berikut.
Url HIVE:

    jdbc:hive2://localhost:49661/

Hasil dari DBeaver:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/cek%20hive%20dengan%20dbeaver.JPG?raw=true)

Setelah proses konversi data ke HIVE, maka data akan di-import ke dalam bentuk DataFrame/RDD menggunakan node **Hive to Spark**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/modeling.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Hive%20to%20Spark%20input%20output.JPG?raw=true)

Node **Hive to Spark** memerlukan 2 input, yaitu:

 - **kotak coklat** (data dari node DB Loader yang benbentuk HIVE)
 - **kotak abu-abu** (Spark context dari node Create Local Big Data Environment)

dan 1 output, yaitu:

 - **kotak hitam** (data yang telah berbentuk DataFrame/RDD)

Selanjutnya, kita akan melakukan ekstraksi data untuk mendapatkan value waktu seperti tahun, bulan, minggu, hari dan sebagainya sesuai dengan **Business Understanding**-nya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/evaluation.JPG?raw=true)

Ada 4 proses yang harus diselesaikan, yaitu:

 - **Extract date-time attributes** (untuk mendapatkan waktu)
 - **Agreagations and time series** (agregasi penggunaan listrik berdasarkan waktu)
 - **Compute daily, day segment percentages** (menghitung persentase dari penggunaan listrik secara per hari, dan pada hari saat periode jam tertentu).
 - **Scatter Plot** (plotting hasil menggunakan algoritma K-Means).

### 1. Extract date-time attributes

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/extract.JPG?raw=true)

#### a. Initial datetime conversion

Pada proses ini akan dibuat kolom2 baru yang berisi waktu2 dari hasi extrasi data.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20SQL%20Query%201.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20SQL%20Query%20%28Initial%20Datetime%20Conversion%29.JPG?raw=true)

SQL Query:

    SELECT 
    
    meterid,
    enc_datetime,
    enc_datetime,
    reading as kw30,
    date_add(cast('2008-12-31' as timestamp), cast(substr(enc_datetime, 1, 3) as int)) as eventDate,
    concat(
		substr(concat("00", cast(cast((cast(substr(enc_datetime, 4) as int) * 30 / 60) as int) %24 as string)), -2, 2),":",
		substr(concat("00", cast(cast(cast(substr(enc_datetime, 4) as int) * 30 % 60 as int) as string)), -2, 2)
	) as my_time 
	
	FROM #table# t1

Pada node **Spark SQL Query** (Initiate datetime conversion) kolom reading akan di-rename dengan nama **kw30** dan akan dibuat 2 kolom baru yaitu **eventDate** dan **my_time**. Berikut penjelasannya:

 - **eventDate**: terbentuk karena memanggil fungsi date_add (mengambil value tanggal yang dimulai pada **31-12-2008** dengan interval waktu yang diambil dari kolom **enc_datetime** dari digit pertama sampai ketiga).
 - **my_time**: mengambil value waktu (format: **hh:mm**) 
 - dimana **hh** dibentuk dari value pada kolom **enc_datetime** yang diambil digit ke-4nya. Lalu dikalikan dengan 30 dan dibagi dengan 60. Setelah itu, akan di-mod dengan 24 yang menandakan 24-jam, dan disimpan hasilnya dalam bentuk 2 digit.
 - untuk **mm**, dibentuk dari value pada kolom **enc_datetime** yang diambil digit ke-4nya. Lalu akan dikali dengan 30 dan di-mod dengan 60 yang menandakan 60 menit, dan disimpan hasilnya dalam bentuk 2 digit. 

Hasilnya adalah sebagai berikut:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20SQL%20Query%20%28Initial%20Datetime%29.JPG?raw=true)

#### b.  Extract new datetime features

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20SQL%20Query%202.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20SQL%20Query%20%28Extract%20New%20Datetime%20Features%29.JPG?raw=true)

    SELECT 
    
    meterid,
    kw30,
    eventDate,
    year(eventDate) as year,
    month(eventDate) as month,
    weekofyear(eventDate) as week,
    date_format(eventDate, 'EEEE') as dayOfWeek,
    hour(my_time) as hour
    
    FROM #table# t1

Untuk proses ini akan dibentuk 5 kolom baru, yaitu:

 - **year**: mengambil nilai tahun dari value pada kolom eventDate.
 - **month**: mengambil nilai bulan dari value pada kolom eventDate.
 -  **week**: mengambil nilai minggu dari value pada kolom eventDate.
 - **dayOfWeek**: mengabil nilai date_format (nama hari) dari value pada kolom eventDate.
 - **hour**: mengambil nilai jam dari value pada kolom my_time.

Berikut adalah hasil query-nya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20SQL%20Query%20%28Extract%20New%20Datetime%20Features%29.JPG?raw=true)

#### c. Assign Weekend/Weekday

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20SQL%20Query%203.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20SQL%20Query%20%28Assign%20Weekend%20Weekday%29.JPG?raw=true)

SQL Query:

    SELECT *, 
    
    CASE 
    WHEN dayOfWeek in ('Saturday','Sunday') 	THEN 'WE' 
    									        ELSE 'BD' 
    									        END as dayClassifier
    									        
    from #table#

Pada proses ini, akan dibuat kolom **dayClassifier** untuk mengkondisikan hari. Ketika nama harinya **Saturday** atau **Sunday**, maka akan diberi value **WE** (Weekend). Selain hari itu akan diberi value **BD**.

Berikut adalah hasilnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20SQL%20Query%20%28Assign%20Weekend%20Weekday%29.JPG?raw=true)

#### d. Assign Hourly Bins (daySegment)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20SQL%20Query%204.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20SQL%20Query%20%28Assign%20Hourly%20Bins%20as%20DaySegment%29.JPG?raw=true)

SQL Query:

    SELECT meterID, kw30, eventDate, year, month, week, dayOfWeek, dayClassifier, hour,
    CASE 
    WHEN hour >=7 AND hour <9 THEN '7-9'
    WHEN hour >=9 AND hour <13 THEN '9-13'
    WHEN hour >=13 AND hour <17 THEN '13-17'  
    WHEN hour >=17 AND hour <21 THEN '17-21' 
    WHEN hour >=21 OR hour <7 THEN '21-7'  
    
    END as daySegment
    
    from #table#

Pada proses ini, akan dibentuk 1 kolom yaitu **daySegment** dengan ketentuan isi sebagai berikut:

 - Jika nilai hour >= 7 dan hour < 9, maka nilai daySegment adalah **7-9**
 - Jika nilai hour >= 9 dan hour < 13, maka nilai daySegment adalah **9-13**
 - Jika nilai hour >= 13 dan hour < 17, maka nilai daySegment adalah **13-17**
 - Jika nilai hour >= 17 dan hour < 21, maka nilai daySegment adalah **17-21**
 - Jika nilai hour >= 21 dan hour < 7, maka nilai daySegment adalah **21-7**

Berikut adalah hasilnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20SQL%20Query%20%28Assign%20Hourly%20Bins%20as%20DaySegment%29.JPG?raw=true)

### 2. Aggregation and Time Series

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/workflow%20%283%29.JPG?raw=true)

Setelah melalui proses pembuatan kolom, maka selanjutnya adalah proses agregasi data. Data akan di Filter sesuai dengan Business Understandingnya.

#### a. Total Usage

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/total%20usage.JPG?raw=true)

Yang pertama, adalah mencari nilai Penggunaan Listrik Total menggunakan node **Spark GroupBy**. Caranya adalah dengan melakukan penjumlahan **(SUM)** pada kolom **kw30**, lalu di Group By berdasarkan **meterID**-nya. 

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20GroupBy%20%28total%20usage%29.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20GroupBy%20%28total%20usage%29%202.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20GroupBy%20%28total%20usage%29.JPG?raw=true)

Agar memudahkan ketika dibaca, maka perlu diubah nama kolomnya menggunakan node **Spark Column Rename**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20Column%20Rename%20%28total%20usage%20as%20totalKW%29.JPG?raw=true)

Nama kolom yang semula **kw30(sum)** diubah menjadi **totalKW**. Proses untuk mencari Total Usage telah selesai. Selanjutnya adalah mencari Rata-rata Usage by Year.

#### b. Usage By Year

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/usage%20by%20year.JPG?raw=true)

Proses ini adalah untuk mencari nilai Rata-rata Penggunaan Listrik per Tahun menggunakan node **Spark GroupBy**. Caranya adalah dengan melakukan penjumlahan **(SUM)** pada kolom **kw30**, lalu di Group By berdasarkan **meterID**, dan **year**-nya. 

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20GroupBy%20%28usage%20by%20year%29.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20GroupBy%20%28usage%20by%20year%29%202.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20GroupBy%20%28usage%20by%20year%29.JPG?raw=true)

Setelah itu, hasilnya akan di proses ke node **Spark GroupBy** untuk mencari nilai rata-ratanya. Caranya adalah dengan melakukan agregasi Average (AVG) pada kolom sum(kw30) lalu di GroupBy berdasarkan meterID.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20GroupBy%20%28average%20by%20year%29.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20GroupBy%20%28average%20by%20year%29%202.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20GroupBy%20%28average%20by%20year%29.JPG?raw=true)

Agar memudahkan ketika dibaca, maka perlu diubah nama kolomnya menggunakan node **Spark Column Rename**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20Column%20Rename%20%28average%20by%20year%20as%20avgYearlyKW%29.JPG?raw=true)

Nama kolom yang semula **mean(sum(kw30))** diubah menjadi **avgYearlyKW**.

Proses untuk mencari nilai Rata-rata dari Penggunaan Listrik per Tahun telah selesai. 

Selanjutnya adalah melakukan Join untuk nilai Penggunaan Listrik Total dan Rata-rata Penggunaan Listrik per Tahun menggunakan node **Spark Joiner**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20Joiner%20%28total%20usage%20+%20avg%20by%20year%29.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20Joiner%20%28total%20usage%20+%20avg%20by%20year%29.JPG?raw=true)

Proses selanjutnya adalah mencari Rata-rata Penggunaan Listrik per Bulan.

#### c. Usage by Month

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/usage%20by%20month.JPG?raw=true)

Proses ini adalah untuk mencari nilai Rata-rata Penggunaan Listrik per Bulan menggunakan node **Spark GroupBy**. Caranya adalah dengan melakukan penjumlahan **(SUM)** pada kolom **kw30**, lalu di Group By berdasarkan **meterID**, **year**, dan **month**-nya. 

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20GroupBy%20%28usage%20by%20month%29.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20GroupBy%20%28usage%20by%20month%29%202.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20GroupBy%20%28usage%20by%20month%29.JPG?raw=true)

Setelah itu, hasilnya akan di proses ke node **Spark GroupBy** untuk mencari nilai rata-ratanya. Caranya adalah dengan melakukan agregasi Average (AVG) pada kolom sum(kw30) lalu di GroupBy berdasarkan meterID.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20GroupBy%20%28average%20by%20month%29.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20GroupBy%20%28average%20by%20month%29%202.JPG?raw=true)

Berikut hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20GroupBy%20%28average%20by%20month%29.JPG?raw=true)

Agar memudahkan ketika dibaca, maka perlu diubah nama kolomnya menggunakan node **Spark Column Rename**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20Column%20Rename%20%28average%20by%20month%20as%20avgMonthlyKW%29.JPG?raw=true)

Nama kolom yang semula **mean(sum(kw30))** diubah menjadi **avgMonthlyKW**.

Proses untuk mencari nilai Rata-rata dari Penggunaan Listrik per Bulan telah selesai. 

Selanjutnya adalah melakukan Join untuk Rata-rata Penggunaan Listrik per Bulan dengan hasil join sebelumnya menggunakan node **Spark Joiner**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20Joiner%20%28total%20usage%20+%20avg%20by%20year%20+%20avg%20by%20month%29.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20Joiner%20%28total%20usage%20+%20avg%20by%20year%20+%20avg%20by%20month%29.JPG?raw=true)

Proses selanjutnya adalah mencari biaya Penggunaan Listrik per Minggu.

#### d. Usage by Week

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/usage%20by%20week.JPG?raw=true)

Proses ini adalah untuk mencari nilai Rata-rata Penggunaan Listrik per Minggu menggunakan node **Spark GroupBy**. Caranya adalah dengan melakukan penjumlahan **(SUM)** pada kolom **kw30**, lalu di Group By berdasarkan **meterID**, **year**, dan **week**-nya. 

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20GroupBy%20%28usage%20by%20week%29.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20GroupBy%20%28usage%20by%20week%29%202.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20GroupBy%20%28usage%20by%20week%29.JPG?raw=true)

Setelah itu, hasilnya akan di proses ke node **Spark GroupBy** untuk mencari nilai rata-ratanya. Caranya adalah dengan melakukan agregasi Average (AVG) pada kolom sum(kw30) lalu di GroupBy berdasarkan meterID.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20GroupBy%20%28average%20by%20week%29.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20GroupBy%20%28average%20by%20week%29%202.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20GroupBy%20%28average%20by%20week%29.JPG?raw=true)

Agar memudahkan ketika dibaca, maka perlu diubah nama kolomnya menggunakan node **Spark Column Rename**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20Column%20Rename%20%28average%20by%20week%20as%20avgWeeklyKW%29.JPG?raw=true)

Nama kolom yang semula **mean(sum(kw30))** diubah menjadi **avgWeeklyKW**.

Proses untuk mencari nilai Rata-rata dari Penggunaan Listrik per Minggu telah selesai. 

Selanjutnya adalah melakukan Join untuk Rata-rata Penggunaan Listrik per Minggu dengan hasil join sebelumnya menggunakan node **Spark Joiner**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20Joiner%20%28total%20usage%20+%20avg%20by%20year%20+%20avg%20by%20month%20+%20avg%20by%20week%29.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20Joiner%20%28total%20usage%20+%20avg%20by%20year%20+%20avg%20by%20month%20+%20avg%20by%20week%29.JPG?raw=true)

Proses selanjutnya adalah mencari biaya Penggunaan Listrik berdasarkan hari dalam Seminggu.

#### e. Usage by Day of Week

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/usage%20by%20day%20of%20week.JPG?raw=true)

Proses ini adalah untuk mencari nilai Rata-rata Penggunaan Listrik berdasarkan hari dalam Seminggu menggunakan node **Spark GroupBy**. Caranya adalah dengan melakukan penjumlahan **(SUM)** pada kolom **kw30**, lalu di Group By berdasarkan **meterID**, **year**, **week**, dan **dayOfWeek**-nya. 

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20GroupBy%20%28usage%20by%20day%20of%20week%29.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20GroupBy%20%28usage%20by%20day%20of%20week%29%202.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20GroupBy%20%28usage%20by%20day%20of%20week%29.JPG?raw=true)

Setelah itu, hasilnya akan di proses ke node **Spark Pivot** untuk mencari nilai rata-rata pada setiap pivot-nya (**dayOfWeek**). Caranya adalah dengan melakukan agregasi Average (AVG) pada kolom sum(kw30) lalu di GroupBy berdasarkan meterID dan pilih pivot column **dayOfWeek**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20Pivot%20%28pivot%20with%20average%29.JPG?raw=true)


![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20Pivot%20%28pivot%20with%20average%29%202.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20Pivot%20%28pivot%20with%20average%29%203.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20Pivot%20%28pivot%20with%20average%29.JPG?raw=true)

Agar memudahkan ketika dibaca, maka perlu diubah nama kolomnya menggunakan node **Spark Column Rename**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20Column%20Rename%20%28Day%20+%20mean%28%29%20to%20avgDay%29.JPG?raw=true)

Nama kolom yang semula **[Hari] + mean(sum(kw30))** diubah menjadi **avg[Hari]**.

Proses untuk mencari nilai Rata-rata dari Penggunaan Listrik berdasarkan hari dalam Seminggu telah selesai. 

Selanjutnya adalah melakukan Join untuk Rata-rata Penggunaan Listrik berdasarkan hari dalam Seminggu dengan hasil join sebelumnya menggunakan node **Spark Joiner**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20Joiner%20%28total%20usage%20+%20avg%20by%20year%20+%20avg%20by%20month%20+%20avg%20by%20week%20+%20avgDay%29.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20Joiner%20%28total%20usage%20+%20avg%20by%20year%20+%20avg%20by%20month%20+%20avg%20by%20week%20+%20avgDay%29.JPG?raw=true)

Proses selanjutnya adalah mencari biaya Penggunaan Listrik per Hari.

#### f. Usage by Day

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/usage%20by%20day.JPG?raw=true)

Proses ini adalah untuk mencari nilai Rata-rata Penggunaan Listrik per Hari menggunakan node **Spark GroupBy**. Caranya adalah dengan melakukan penjumlahan **(SUM)** pada kolom **kw30**, lalu di Group By berdasarkan **meterID**, dan **eventDate**-nya. 

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20GroupBy%20%28usage%20by%20day%29.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20GroupBy%20%28usage%20by%20day%29%202.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20GroupBy%20%28usage%20by%20day%29.JPG?raw=true)

Setelah itu, hasilnya akan di proses ke node **Spark GroupBy** untuk mencari nilai rata-ratanya. Caranya adalah dengan melakukan agregasi Average (AVG) pada kolom sum(kw30) lalu di GroupBy berdasarkan meterID.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20GroupBy%20%28average%20by%20day%29.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20GroupBy%20%28average%20by%20day%29%202.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20GroupBy%20%28average%20by%20day%29.JPG?raw=true)

Agar memudahkan ketika dibaca, maka perlu diubah nama kolomnya menggunakan node **Spark Column Rename**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20Column%20Rename%20%28average%20by%20day%20as%20avgDaily%29.JPG?raw=true)

Nama kolom yang semula **mean(sum(kw30))** diubah menjadi **avgDaily**.

Proses untuk mencari nilai Rata-rata dari Penggunaan Listrik per Hari telah selesai. 

Selanjutnya adalah melakukan Join untuk Rata-rata Penggunaan Listrik per Hari dengan hasil join sebelumnya menggunakan node **Spark Joiner**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20Joiner%20%28total%20usage%20+%20avg%20by%20year%20+%20avg%20by%20month%20+%20avg%20by%20week%20+%20avgDay%20+%20avgDaily%29.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20Joiner%20%28total%20usage%20+%20avg%20by%20year%20+%20avg%20by%20month%20+%20avg%20by%20week%20+%20avgDay%20+%20avgDaily%29.JPG?raw=true)

Proses selanjutnya adalah mencari Penggunaan Listrik per Hari pada periode jam tertentu.

#### g. Usage by Day Segment

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/usage%20by%20day%20segment.JPG?raw=true)

Proses ini adalah untuk mencari nilai Rata-rata Penggunaan Listrik per Hari menggunakan node **Spark GroupBy**. Caranya adalah dengan melakukan penjumlahan **(SUM)** pada kolom **kw30**, lalu di Group By berdasarkan **meterID**, **eventDate**, dan **daySegment**-nya. 

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20GroupBy%20%28usage%20by%20day%20segment%29.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20GroupBy%20%28usage%20by%20day%20segment%29%202.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20GroupBy%20%28usage%20by%20day%20segment%29.JPG?raw=true)

Setelah itu, hasilnya akan di proses ke node **Spark Pivot** untuk mencari nilai rata-rata pada setiap pivot-nya (**daySegment**). Caranya adalah dengan melakukan agregasi Average (AVG) pada kolom sum(kw30) lalu di GroupBy berdasarkan meterID dan pilih pivot column **daySegment**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20Pivot%20%28pivot%20with%20day%20segment%29.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20Pivot%20%28pivot%20with%20day%20segment%29%202.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20Pivot%20%28pivot%20with%20day%20segment%29%203.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20Pivot%20%28pivot%20with%20day%20segment%29.JPG?raw=true)

Agar memudahkan ketika dibaca, maka perlu diubah nama kolomnya menggunakan node **Spark Column Rename**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20Column%20Rename%20%28average%20by%20day%20segment%29.JPG?raw=true)

Nama kolom yang semula **[daySegment] + mean(sum(kw30))** diubah menjadi **avg[daySegment]**.

Proses untuk mencari nilai Rata-rata dari Penggunaan Listrik per Hari pada periode jam tertentu telah selesai. 

Selanjutnya adalah melakukan Join untuk Rata-rata Penggunaan Listrik per Hari pada periode jam tertentu dengan hasil join sebelumnya menggunakan node **Spark Joiner**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20Joiner%20%28total%20usage%20+%20avg%20by%20year%20+%20avg%20by%20month%20+%20avg%20by%20week%20+%20avgDay%20+%20avgDaily%20+%20avgDaySegment%29.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20Joiner%20%28total%20usage%20+%20avg%20by%20year%20+%20avg%20by%20month%20+%20avg%20by%20week%20+%20avgDay%20+%20avgDaily%20+%20avgDaySegment%29.JPG?raw=true)

Proses selanjutnya adalah mencari Penggunaan Listrik pada saat Weekend dan Weekday.

#### h. Usage by Day Classifier

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/usage%20by%20day%20classifier.JPG?raw=true)

Proses ini adalah untuk mencari nilai Rata-rata Penggunaan Listrik per Hari menggunakan node **Spark GroupBy**. Caranya adalah dengan melakukan penjumlahan **(SUM)** pada kolom **kw30**, lalu di Group By berdasarkan **meterID**, **year**, **month**, **week**, dan **dayClassifier**-nya. 

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20GroupBy%20%28usage%20by%20day%20classifier%29.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20GroupBy%20%28usage%20by%20day%20classifier%29%202.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20GroupBy%20%28usage%20by%20day%20classifier%29.JPG?raw=true)

Setelah itu, hasilnya akan di proses ke node **Spark Pivot** untuk mencari nilai rata-rata pada setiap pivot-nya (**dayClassifier**). Caranya adalah dengan melakukan agregasi Average (AVG) pada kolom sum(kw30) lalu di GroupBy berdasarkan meterID dan pilih pivot column **dayClassifier**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20Pivot%20%28pivot%20with%20day%20classifier%29.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20Pivot%20%28pivot%20with%20day%20classifier%29%202.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20Pivot%20%28pivot%20with%20day%20classifier%29%203.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20Pivot%20%28pivot%20with%20day%20classifier%29.JPG?raw=true)

Agar memudahkan ketika dibaca, maka perlu diubah nama kolomnya menggunakan node **Spark Column Rename**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20Column%20Rename%20%28average%20by%20day%20classifier%29.JPG?raw=true)

Nama kolom yang semula **[dayClassifier] + mean(sum(kw30))** diubah menjadi **avg[dayClassifier]**.

Proses untuk mencari nilai Rata-rata dari Penggunaan Listrik pada saat Weekend dan Weekday telah selesai. 

Selanjutnya adalah melakukan Join untuk Rata-rata Penggunaan Listrik pada saat Weekend dan Weekday dengan hasil join sebelumnya menggunakan node **Spark Joiner**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20Joiner%20%28total%20usage%20+%20avg%20by%20year%20+%20avg%20by%20month%20+%20avg%20by%20week%20+%20avgDay%20+%20avgDaily%20+%20avgDaySegment%20+%20avgClassifier%29.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20Joiner%20%28total%20usage%20+%20avg%20by%20year%20+%20avg%20by%20month%20+%20avg%20by%20week%20+%20avgDay%20+%20avgDaily%20+%20avgDaySegment%20+%20avgClassifier%29.JPG?raw=true)

Proses selanjutnya adalah mencari Penggunaan Listrik pada jam tertentu.

#### i. Usage by Hour

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/usage%20by%20hour.JPG?raw=true)

Proses ini adalah untuk mencari nilai Rata-rata Penggunaan Listrik per Hari menggunakan node **Spark GroupBy**. Caranya adalah dengan melakukan penjumlahan **(SUM)** pada kolom **kw30**, lalu di Group By berdasarkan **meterID**, **eventDate**, dan **hour**-nya. 

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20GroupBy%20%28usage%20by%20hour%29.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20GroupBy%20%28usage%20by%20hour%29%202.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20GroupBy%20%28usage%20by%20hour%29.JPG?raw=true)

Setelah itu, hasilnya akan di proses ke node **Spark GroupBy** untuk mencari nilai rata-ratanya. Caranya adalah dengan melakukan agregasi Average (AVG) pada kolom sum(kw30) lalu di GroupBy berdasarkan meterID.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20GroupBy%20%28average%20by%20hour%29.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20GroupBy%20%28average%20by%20hour%29%202.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20GroupBy%20%28average%20by%20hour%29.JPG?raw=true)

Agar memudahkan ketika dibaca, maka perlu diubah nama kolomnya menggunakan node **Spark Column Rename**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20Column%20Rename%20%28avgHourly%29.JPG?raw=true)

Nama kolom yang semula **mean(sum(kw30))** diubah menjadi **avgHourly**.

Proses untuk mencari nilai Rata-rata dari Penggunaan Listrik pada jam tertentu. telah selesai. 

Selanjutnya adalah melakukan Join untuk Rata-rata Penggunaan Listrik pada jam tertentu. dengan hasil join sebelumnya menggunakan node **Spark Joiner**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20Joiner%20%28total%20usage%20+%20avg%20by%20year%20+%20avg%20by%20month%20+%20avg%20by%20week%20+%20avgDay%20+%20avgDaily%20+%20avgDaySegment%20+%20avgClassifier%20+%20avgHourly%29.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20Joiner%20%28total%20usage%20+%20avg%20by%20year%20+%20avg%20by%20month%20+%20avg%20by%20week%20+%20avgDay%20+%20avgDaily%20+%20avgDaySegment%20+%20avgClassifier%20+%20avgHourly%29.JPG?raw=true)

Proses selanjutnya adalah menghitung presentase dari penggunakan listrik per hari dan pada periode jam tertentu.

### 3. Compute daily, day segment percentages
Pada proses ini, akan dihitung persentase dari penggunaan listrik secara per hari, dan pada hari saat periode jam tertentu pada proses sebelumnya menggunakan node **Spark SQL Query**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/compute.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20SQL%20Query%20%28Compute%20daily,%20day%20segment%20percentage%29.JPG?raw=true)

SQL Query:

    SELECT `meterID`, `totalKW`, `avgYearlyKW`,`avgMonthlyKW`,`avgWeeklyKW`,
       `avgMonday`,`avgTuesday`,`avgWednesday`,`avgThursday`,`avgFriday`,`avgSaturday`,`avgSunday`,
       `avgDaily`,`avg_7to9`,`avg_9to13`,`avg_13to17`,`avg_17to21`,`avg_21to7`,`avg_BD`,`avg_WE`,`avgHourly`,
       (avgMonday / avgWeeklyKW) * 100.0 as pctMonday,
       (avgTuesday / avgWeeklyKW) * 100.0 as pctTuesday,
       (avgWednesday / avgWeeklyKW) * 100.0 as pctWednesday,
       (avgThursday / avgWeeklyKW) * 100.0 as pctThursday,
       (avgFriday / avgWeeklyKW) * 100.0 as pctFriday,
       (avgSaturday / avgWeeklyKW) * 100.0 as pctSaturday,
       (avgSunday / avgWeeklyKW) * 100.0 as pctSunday,
       (avg_7to9 / avgDaily) * 100.0 as pct_7to9,
       (avg_9to13 / avgDaily) * 100.0 as pct_9to13,
       (avg_13to17 / avgDaily) * 100.0 as pct_13to17,
       (avg_17to21 / avgDaily) * 100.0 as pct_17to21,
       (avg_21to7 / avgDaily) * 100.0 as pct_21to7
       
       FROM #table#
       
Pada Query di atas, akan dibuat 12 kolom yang merepresentasikan presentasi dari Penggunaan Listrik per hari dan pada periode jam tertentu.

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20SQL%20Query%20%28Compute%20daily,%20day%20segment%20percentage%29.JPG?raw=true)

Proses selanjutnya adalah Plotting menggunakan algoritma PCA dan K-Means.

## 4. Modelling
Pada proses ini akan dilakukan pemodelan data menggunakan algoritma K-Means dan PCA.
![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/scatter.JPG?raw=true)


![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/metanode.JPG?raw=true)

Data akan dinormalisasi terlebih dahulu sebelum proses pemodelan menggunakan algoritma K-Means dan PCA. 

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/normalisasi%20config.JPG?raw=true)

Normalisasi menggunakan nilai minimum 0.0 dan maksimum 1.0. Berikut ini adalah hasilnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/normalisasi%20hasil.JPG?raw=true)

Lalu, data yang telah dinormalisasi akan di-model dengan algoritma PCA.
Berikut adalah konfigurasinya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/pca%20config.JPG?raw=true)

Algoritma PCA adalah algoritma untuk **mereduksi dimensi** (mengurangi jumlah kolom) dari sebuah dataset sehingga data tersebut dapat divisualisasikan jika terdapat cluster atau class tertentu. Untuk lebih detailnya, bisa mengunjungi link berikut : [PCA Algorithm](https://en.wikipedia.org/wiki/Principal_component_analysis).

Berikut adalah hasilnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/pca%20hasil.JPG?raw=true)

Selanjutnya adalah pemodelan data menggunakan algoritma K-Means. Berikut adalah konfigurasinya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/k-means%20config.JPG?raw=true)

Pada konfigurasi tersebut, akan dibuat 3 cluster berdasarkan data yang ada.
Berikut adalah hasilnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/k-means%20hasil.JPG?raw=true)

Selanjutnya adalah melakukan filtrasi kolom pada hasil modeling data menggunakan K-Means untuk mengambil id dan Cluster-nya saja. Hal ini dikarenakan, model data akan dijoin dengan hasil pemodelan menggunakan algoritma PCA.

Untuk melakukan filtrasi, diperlukan node **Spark Column Filter**.

Berikut adalah konfigurasi dan hasilnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/spark%20column%20filter%20config.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20spark%20column%20filter.JPG?raw=true)



Setelah proses pemodelan untuk kedua algoritma selesai, maka hasil pemodelan tersebut akan di-joinkan menggunakan node **Spark Joiner**.

Berikut adalah hasilnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20spark%20joiner%20%28k-means%29.JPG?raw=true)

Lalu, selanjutnya data model akan diubah ke format table dan di-denormalisasi agar dapat dilakukan proses evaluasi.

Untuk mengubah data ke format table dapat menggunakan node **Spark to Table**. Dan untuk melakukan denormalisasi data, dapat menggunakan node **Denormalizer PMML**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/spark%20to%20table%20%28untuk%20visualisasi%29.JPG?raw=true)


![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/denormalisasi.JPG?raw=true)

Setelah itu, model data telah siap untuk proses Evaluasi.

## 5. Evaluation

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/evaluation1.JPG?raw=true)

Pada tahap ini, data yang telah dimodelkan menggunakan algoritma PCA dan K-Means akan di-evaluasi dengan cara di Plot menggunakan node **Scatter Plot**.

Sebelum masuk ke node **Scatter Plot**, format angka pada data harus diubah ke format string, agar proses pemberian warna pada data lebih jelas.
Proses tersebut ada pada node **Number To String** walaupun node ini telah **(deprecated/kadaluarsa)**. 

Setelah data diubah ke format string, maka data akan diberi warna menggunakan node **Color Manager**. Lalu data akan diproses menggunakan **Scatter Plot**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/scatter%20plot%20config.JPG?raw=true)

Pada konfigurasi di atas, kolom yang dipilih untuk diplot adalah PCA dimension 0 dan dimension 1.

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20PCA,%20K-Means,%20Scatter%20Plot.JPG?raw=true)

Selain menggunakan **Scatter Plot**, data akan dievaluasi dengan node **Table View**. Berikut adalah hasilnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20PCA,%20K-Means,%20Scatter%20Plot%20%282%29.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20PCA,%20K-Means,%20Scatter%20Plot%20%283%29.JPG?raw=true)

Proses Evaluation telah selesai.

## 6. Deployment

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/deployment1.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/deployment.JPG?raw=true)

Pada proses ini, data hasil dari Evaluation akan di-deploy pada HIVE dan Parquet menggunakan node **Spark to Hive** dan **Spark to Parquet**.

Untuk Spark to Hive, berikut adalah konfigurasinya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20to%20Hive%20Config.JPG?raw=true)

tabel akan dinamai **metricaggs**. Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20to%20Hive.JPG?raw=true)

Jika kita cek menggunakan DBeaver, maka hasilnya adalah sebagai berikut:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20to%20Hive%20pada%20DBeaver.JPG?raw=true)

Untuk Spark to Parquet, konfigurasinya adalah sebagai berikut:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20to%20Parquet%20Config.JPG?raw=true)

Hasilnya akan disimpan pada direktori **meter_aggs**. **Spark to Parquet** berfungsi untuk mengubah format data yang awalnya adalah **DataFrame/RDD** menjadi format **file Parquet**. Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/hasil%20Spark%20to%20Parquet.JPG?raw=true)

Terima Kasih.
