
# Analisis Time Series (Produksi Beer Bulanan) menggunakan Spark dan KNIME

Berikut adalah workflow KNIME-nya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/workflow.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Electric%20Production/dokum/workflow2.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Electric%20Production/dokum/workflow3.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Electric%20Production/dokum/workflow4.JPG?raw=true)

## 1. Business Understanding
Menganalisis Rata-rata Suhu pada kurun waktu tertentu (time series), yaitu:

 1. **Total Usage**:  Total Produksi Beer.
 2. **Usage by Year**: Produksi Beer per Tahun.
 3. **Usage by Month**: Produksi Beer per Bulan.
 4. **Usage by Week**: Produksi Beer per Minggu.
 5. **Usage by Day of Week**: Produksi Beer berdasarkan hari dalam Seminggu.
 6. **Usage by Day**: Produksi Beer per Hari.
 7. **Usage by Day Classifier**: Produksi Beer pada saat Weekend dan Weekday.

## 2. Data Understanding

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/data%20preparation.JPG?raw=true)

Sebelum dilakukan proses ekstraksi data, dataset harus disesuaikan formatnya agar proses agregasi tidak terjadi error.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/data%20preparation2.JPG?raw=true)

Dataset-nya terdiri dari 3 kolom yaitu:

 1. **id**: id produksi beer berdasarkan tahun.
 2. **DATE**: tanggal tercatatnya jumlah produksi beer .
 3. **Monthly_beer_production**: jumlah produksi beer pada waktu tertentu.

## 3. Data Preparation
Dataset yang digunakan adalah dataset daily minimum temperatures. Data diload menggunakan node **File Reader**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/data%20preparation%282%29.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/data%20preparation2.JPG?raw=true)

Lalu data akan di-ubah formatnya menjadi DataFrame/RDD agar dapat dilakukan query menggunakan Spark.

Karena prosesnya menggunakan Spark, maka kita perlu menggunakan node **Create Local Big Data Environment**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/environment.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/modeling.JPG?raw=true)


![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/modeling%282%29.JPG?raw=true)

Dataset yang sebelumnya sudah diload ke **File Reader**, akan diload ke HIVE menggunakan metanode **Load Data**.

Di dalam metanode Load Data, terdapat 2 proses, yaitu **DB Table Creator** dan **DB Loader**. Pada node **DB Table Creator**, table HIVE akan diberi nama **meter**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/db%20table%20creator%20config.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/db%20table%20creator%20input%20output.JPG?raw=true)

Node **DB Table Creator** memerlukan 2 input, yaitu: 

 - **kotak merah** (DB Connection dimana disini menggunakan Spark HIVE)
 -  **segitiga garis hitam** (berasal dari File Reader)

Sedangkan untuk output-nya adalah kotak merah yang menandakan DB Connection yang telah berhasil dibuat.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/db%20loader%20config.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/db%20loader%20input%20output.JPG?raw=true)

Lalu pada node **DB Loader**, memerlukan 3 input yaitu:

 - **segitiga hitam** (berasal dari File Reader).
 - **kotak merah** (DB Connection dari DB Table Creator).
 - **kotak garis biru** (Koneksi HIVE dari node Create Local Big Data Environment).

dan 1 output, yaitu:

 - **kotak coklat** (data yang telah berbentuk HIVE).

Ketika di-execute, maka hasilnya akan seperti berikut.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/db%20loader%20hasil.JPG?raw=true)

Jika ingin mengeceknya HIVE-nya menggunakan DBeaver, maka hasilnya seperti berikut.
Url HIVE:

    jdbc:hive2://localhost:63192/

Hasil dari DBeaver:

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/db%20loader%20hasil%20dbeaver.JPG?raw=true)

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
 - **Agreagations and time series** (agregasi Produksi Beer berdasarkan waktu)
 - **Compute daily, day segment percentages** (menghitung persentase dari Produksi Beer secara per hari, dan pada hari saat periode jam tertentu).
 - **Scatter Plot** (plotting hasil dari algoritma K-Means).

### 1. Extract date-time attributes

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Daily%20Minimum%20Temperatures/dokum/workflow2.JPG?raw=true)

#### a. Initial datetime conversion

Pada proses ini akan dibuat kolom2 baru yang berisi waktu2 dari hasi extrasi data.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20SQL%20Query%201.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/spark%20sql%20query%20init.JPG?raw=true)

SQL Query:

    SELECT
    id, 
    DATE,
    cast(Monthly_beer_production as double) as production,
    cast(Date as timestamp) as eventDate
    
    FROM #table# t1

Pada node **Spark SQL Query** (Initiate datetime conversion) kolom Daily_minimum_temperatures akan diubah formatnya menjadi double. 

Lalu akan dibuat 1 kolom baru yaitu **eventDate** yang mengambil value Date menjadi timestamp.

Hasilnya adalah sebagai berikut:

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/spark%20sql%20query%20init%20hasil.JPG?raw=true)

#### b.  Extract new datetime features

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/Spark%20SQL%20Query%202.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/spark%20sql%20query%20extract.JPG?raw=true)

    SELECT 
    id,
    DATE,
    production,
    eventDate,
    year(eventDate) as year,
    month(eventDate) as month,
    weekofyear(eventDate) as week,
    date_format(eventDate, 'EEEE') as dayOfWeek
    
    FROM #table# t1

Untuk proses ini akan dibentuk 4 kolom baru, yaitu:

 - **year**: mengambil nilai tahun dari value pada kolom eventDate.
 - **month**: mengambil nilai bulan dari value pada kolom eventDate.
 -  **week**: mengambil nilai minggu dari value pada kolom eventDate.
 - **dayOfWeek**: mengabil nilai date_format (nama hari) dari value pada kolom eventDate.

Berikut adalah hasil query-nya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/spark%20sql%20query%20extract%20hasil.JPG?raw=true)

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

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/spark%20sql%20query%20assign%20hasil.JPG?raw=true)

### 2. Aggregation and Time Series

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/workflow%20%283%29.JPG?raw=true)

Setelah melalui proses pembuatan kolom, maka selanjutnya adalah proses agregasi data. Data akan di Filter sesuai dengan Business Understandingnya.

#### a. Total Usage

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/total%20usage.JPG?raw=true)

Yang pertama, adalah mencari nilai Produksi Beer Total menggunakan node **Spark GroupBy**. Caranya adalah dengan melakukan penjumlahan **(SUM)** pada kolom **production**, lalu di Group By berdasarkan **id**-nya. 

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/total%20usage%20config.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/total%20usage%20config2.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/total%20usage%20hasil.JPG?raw=true)

Agar memudahkan ketika dibaca, maka perlu diubah nama kolomnya menggunakan node **Spark Column Rename**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Electric%20Production/dokum/total%20usage%20rename.JPG?raw=true)

Nama kolom yang semula **production** diubah menjadi **total**. Proses untuk mencari Total Usage telah selesai. Selanjutnya adalah mencari Rata-rata Usage by Year.

#### b. Usage By Year

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/usage%20by%20year.JPG?raw=true)

Proses ini adalah untuk mencari nilai Rata-rata Produksi Beer per Tahun menggunakan node **Spark GroupBy**. Caranya adalah dengan melakukan penjumlahan **(SUM)** pada kolom **production**, lalu di Group By berdasarkan **id**, dan **year**-nya. 

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/usage%20by%20year%20config.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/usage%20by%20year%20config2.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/usage%20by%20year%20hasil.JPG?raw=true)

Setelah itu, hasilnya akan di proses ke node **Spark GroupBy** untuk mencari nilai rata-ratanya. Caranya adalah dengan melakukan agregasi Average (AVG) pada kolom sum(production) lalu di GroupBy berdasarkan id.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Electric%20Production/dokum/avg%20by%20year%20config.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Electric%20Production/dokum/avg%20by%20year%20config2.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/avg%20by%20year%20hasil.JPG?raw=true)

Agar memudahkan ketika dibaca, maka perlu diubah nama kolomnya menggunakan node **Spark Column Rename**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Electric%20Production/dokum/avg%20by%20year%20rename.JPG?raw=true)

Nama kolom yang semula **mean(sum(production))** diubah menjadi **avgYearly**.

Proses untuk mencari nilai Rata-rata dari Produksi Beer per Tahun telah selesai. 

Selanjutnya adalah melakukan Join untuk nilai Produksi Beer Total dan Rata-rata Produksi Beer per Tahun menggunakan node **Spark Joiner**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Daily%20Minimum%20Temperatures/dokum/spark%20joiner%20config.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/spark%20joiner%20hasil%201.JPG?raw=true)

Proses selanjutnya adalah mencari Rata-rata Produksi Beer per Bulan.

#### c. Usage by Month

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/usage%20by%20month.JPG?raw=true)

Proses ini adalah untuk mencari nilai Rata-rata Produksi Beer per Bulan menggunakan node **Spark GroupBy**. Caranya adalah dengan melakukan penjumlahan **(SUM)** pada kolom **production**, lalu di Group By berdasarkan **id**, **year**, dan **month**-nya. 

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/usage%20by%20month%20config.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/usage%20by%20month%20config2.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/usage%20by%20month%20hasil.JPG?raw=true)

Setelah itu, hasilnya akan di proses ke node **Spark GroupBy** untuk mencari nilai rata-ratanya. Caranya adalah dengan melakukan agregasi Average (AVG) pada kolom sum(production) lalu di GroupBy berdasarkan id.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Electric%20Production/dokum/avg%20by%20month%20config.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Electric%20Production/dokum/avg%20by%20month%20config2.JPG?raw=true)

Berikut hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/avg%20by%20month%20hasil.JPG?raw=true)

Agar memudahkan ketika dibaca, maka perlu diubah nama kolomnya menggunakan node **Spark Column Rename**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Electric%20Production/dokum/avg%20by%20month%20rename.JPG?raw=true)

Nama kolom yang semula **mean(sum(production))** diubah menjadi **avgMonthly**.

Proses untuk mencari nilai Rata-rata dari Produksi Beer per Bulan telah selesai. 

Selanjutnya adalah melakukan Join untuk Rata-rata Produksi Beer per Bulan dengan hasil join sebelumnya menggunakan node **Spark Joiner**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Daily%20Minimum%20Temperatures/dokum/spark%20joiner%20config.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/spark%20joiner%20hasil%202.JPG?raw=true)

Proses selanjutnya adalah mencari Rata-rata Produksi Beer per Minggu.

#### d. Usage by Week

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/usage%20by%20week.JPG?raw=true)

Proses ini adalah untuk mencari nilai Rata-rata Produksi Beer per Minggu menggunakan node **Spark GroupBy**. Caranya adalah dengan melakukan penjumlahan **(SUM)** pada kolom **production**, lalu di Group By berdasarkan **id**, **year**, dan **week**-nya. 

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/usage%20by%20week%20config.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/usage%20by%20week%20config2.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/usage%20by%20week%20hasil.JPG?raw=true)

Setelah itu, hasilnya akan di proses ke node **Spark GroupBy** untuk mencari nilai rata-ratanya. Caranya adalah dengan melakukan agregasi Average (AVG) pada kolom sum(production) lalu di GroupBy berdasarkan id.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Electric%20Production/dokum/avg%20by%20week%20config.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Electric%20Production/dokum/avg%20by%20week%20config2.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/avg%20by%20week%20hasil.JPG?raw=true)

Agar memudahkan ketika dibaca, maka perlu diubah nama kolomnya menggunakan node **Spark Column Rename**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Electric%20Production/dokum/avg%20by%20week%20rename.JPG?raw=true)

Nama kolom yang semula **mean(sum(production))** diubah menjadi **avgWeekly**.

Proses untuk mencari nilai Rata-rata dari Produksi Beer per Minggu telah selesai. 

Selanjutnya adalah melakukan Join untuk Rata-rata Produksi Beer per Minggu dengan hasil join sebelumnya menggunakan node **Spark Joiner**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Daily%20Minimum%20Temperatures/dokum/spark%20joiner%20config.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/spark%20joiner%20hasil%203.JPG?raw=true)

Proses selanjutnya adalah mencari Rata-rata Produksi Beer berdasarkan hari dalam Seminggu.

#### e. Usage by Day of Week

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/usage%20by%20day%20of%20week.JPG?raw=true)

Proses ini adalah untuk mencari nilai Rata-rata Produksi Beer berdasarkan hari dalam Seminggu menggunakan node **Spark GroupBy**. Caranya adalah dengan melakukan penjumlahan **(SUM)** pada kolom **production**, lalu di Group By berdasarkan **id**, **year**, **week**, dan **dayOfWeek**-nya. 

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/usage%20by%20day%20of%20week%20config.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/usage%20by%20day%20of%20week%20config2.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/usage%20by%20day%20of%20week%20hasil.JPG?raw=true)

Setelah itu, hasilnya akan di proses ke node **Spark Pivot** untuk mencari nilai rata-rata pada setiap pivot-nya (**dayOfWeek**). Caranya adalah dengan melakukan agregasi Average (AVG) pada kolom sum(production) lalu di GroupBy berdasarkan id dan pilih pivot column **dayOfWeek**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Electric%20Production/dokum/pivot%20day%20of%20week%20config.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Electric%20Production/dokum/pivot%20day%20of%20week%20config2.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Electric%20Production/dokum/pivot%20day%20of%20week%20config3.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/pivot%20day%20of%20week%20hasil.JPG?raw=true)

Agar memudahkan ketika dibaca, maka perlu diubah nama kolomnya menggunakan node **Spark Column Rename**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Electric%20Production/dokum/pivot%20day%20of%20week%20rename.JPG?raw=true)

Nama kolom yang semula **[Hari] + mean(sum(production))** diubah menjadi **avg[Hari]**.

Proses untuk mencari nilai Rata-rata dari Produksi Beer berdasarkan hari dalam Seminggu telah selesai. 

Selanjutnya adalah melakukan Join untuk Rata-rata Produksi Beer berdasarkan hari dalam Seminggu dengan hasil join sebelumnya menggunakan node **Spark Joiner**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Daily%20Minimum%20Temperatures/dokum/spark%20joiner%20config.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/spark%20joiner%20hasil%204.JPG?raw=true)

Proses selanjutnya adalah mencari Produksi Beer per Hari.

#### f. Usage by Day

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/usage%20by%20day.JPG?raw=true)

Proses ini adalah untuk mencari nilai Rata-rata Produksi Beer per Hari menggunakan node **Spark GroupBy**. Caranya adalah dengan melakukan penjumlahan **(SUM)** pada kolom **production**, lalu di Group By berdasarkan **id**, dan **eventDate**-nya. 

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/usage%20by%20day%20config.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/usage%20by%20day%20config2.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/usage%20by%20day%20hasil.JPG?raw=true)

Setelah itu, hasilnya akan di proses ke node **Spark GroupBy** untuk mencari nilai rata-ratanya. Caranya adalah dengan melakukan agregasi Average (AVG) pada kolom sum(production) lalu di GroupBy berdasarkan id.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Electric%20Production/dokum/avg%20by%20day%20config.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Electric%20Production/dokum/avg%20by%20day%20config2.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/avg%20by%20day%20hasil.JPG?raw=true)

Agar memudahkan ketika dibaca, maka perlu diubah nama kolomnya menggunakan node **Spark Column Rename**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Electric%20Production/dokum/avg%20by%20day%20rename.JPG?raw=true)

Nama kolom yang semula **mean(sum(production))** diubah menjadi **avgDaily**.

Proses untuk mencari nilai Rata-rata dari Produksi Beer per Hari telah selesai. 

Selanjutnya adalah melakukan Join untuk Rata-rata Produksi Beer per Hari dengan hasil join sebelumnya menggunakan node **Spark Joiner**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Daily%20Minimum%20Temperatures/dokum/spark%20joiner%20config.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/spark%20joiner%20hasil%205.JPG?raw=true)

Proses selanjutnya adalah mencari Produksi Beer pada saat Weekend dan Weekday.

#### h. Usage by Day Classifier

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/usage%20by%20day%20classifier.JPG?raw=true)

Proses ini adalah untuk mencari nilai Rata-rata Produksi Beer pada Weekend dan Weekday menggunakan node **Spark GroupBy**. Caranya adalah dengan melakukan penjumlahan **(SUM)** pada kolom **production**, lalu di Group By berdasarkan **id**, **year**, **month**, **week**, dan **dayClassifier**-nya. 

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/usage%20by%20day%20class%20config.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/usage%20by%20day%20class%20config2.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/usage%20by%20day%20class%20hasil.JPG?raw=true)

Setelah itu, hasilnya akan di proses ke node **Spark Pivot** untuk mencari nilai rata-rata pada setiap pivot-nya (**dayClassifier**). Caranya adalah dengan melakukan agregasi Average (AVG) pada kolom sum(production) lalu di GroupBy berdasarkan id dan pilih pivot column **dayClassifier**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Electric%20Production/dokum/pivot%20day%20class%20config.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Electric%20Production/dokum/pivot%20day%20class%20config2.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Electric%20Production/dokum/pivot%20day%20class%20config3.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/pivot%20day%20class%20hasil.JPG?raw=true)

Agar memudahkan ketika dibaca, maka perlu diubah nama kolomnya menggunakan node **Spark Column Rename**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Electric%20Production/dokum/pivot%20day%20class%20rename.JPG?raw=true)

Nama kolom yang semula **[dayClassifier] + mean(sum(production))** diubah menjadi **avg[dayClassifier]**.

Proses untuk mencari nilai Rata-rata dari Produksi Beer pada saat Weekend dan Weekday telah selesai. 

Selanjutnya adalah melakukan Join untuk Rata-rata Produksi Beer pada saat Weekend dan Weekday dengan hasil join sebelumnya menggunakan node **Spark Joiner**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Daily%20Minimum%20Temperatures/dokum/spark%20joiner%20config.JPG?raw=true)

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/spark%20joiner%20hasil%206.JPG?raw=true)

Proses selanjutnya adalah menghitung presentase dari produksi beer per hari.

### 3. Compute daily, day segment percentages
Pada proses ini, akan dihitung persentase dari Produksi Beer secara per hari, dan pada hari saat periode jam tertentu pada proses sebelumnya menggunakan node **Spark SQL Query**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/compute.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Daily%20Minimum%20Temperatures/dokum/spark%20query%20compute.JPG?raw=true)

SQL Query:

    SELECT `id`, `total`, `avgYearly`,`avgMonthly`,`avgWeekly`,
       `avgMonday`,`avgTuesday`,`avgWednesday`,`avgThursday`,`avgFriday`,`avgSaturday`,`avgSunday`,
       `avgDaily`,`avg_BD`,`avg_WE`,
       (avgMonday / avgWeekly) * 100.0 as pctMonday,
       (avgTuesday / avgWeekly) * 100.0 as pctTuesday,
       (avgWednesday / avgWeekly) * 100.0 as pctWednesday,
       (avgThursday / avgWeekly) * 100.0 as pctThursday,
       (avgFriday / avgWeekly) * 100.0 as pctFriday,
       (avgSaturday / avgWeekly) * 100.0 as pctSaturday,
       (avgSunday / avgWeekly) * 100.0 as pctSunday
       
       FROM #table#
       
Pada Query di atas, akan dibuat 7 kolom yang merepresentasikan presentasi dari Suhu minimum per hari.

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/spark%20sql%20query%20compute%20hasil.JPG?raw=true)

Proses selanjutnya adalah Plotting menggunakan algoritma PCA dan K-Means.

## 3. Modelling
Pada proses ini akan dilakukan pemodelan data menggunakan algoritma K-Means dan PCA.
![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/scatter.JPG?raw=true)


![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/metanode.JPG?raw=true)

Data akan dinormalisasi terlebih dahulu sebelum proses pemodelan menggunakan algoritma K-Means dan PCA. 

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/normalisasi%20config.JPG?raw=true)

Normalisasi menggunakan nilai minimum 0.0 dan maksimum 1.0. Berikut ini adalah hasilnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/normalisasi%20hasil.JPG?raw=true)

Lalu, data yang telah dinormalisasi akan di-model dengan algoritma PCA.
Berikut adalah konfigurasinya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/pca%20config.JPG?raw=true)

Algoritma PCA adalah algoritma untuk **mereduksi dimensi** (mengurangi jumlah kolom) dari sebuah dataset sehingga data tersebut dapat divisualisasikan jika terdapat cluster atau class tertentu. Untuk lebih detailnya, bisa mengunjungi link berikut : [PCA Algorithm](https://en.wikipedia.org/wiki/Principal_component_analysis).

Berikut adalah hasilnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/pca%20hasil.JPG?raw=true)

Selanjutnya adalah pemodelan data menggunakan algoritma K-Means. Berikut adalah konfigurasinya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/kmeans%20config.JPG?raw=true)

Pada konfigurasi tersebut, akan dibuat 3 cluster berdasarkan data yang ada.
Berikut adalah hasilnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Electric%20Production/dokum/kmeans%20hasil.JPG?raw=true)

Selanjutnya adalah melakukan filtrasi kolom pada hasil modeling data menggunakan K-Means untuk mengambil id dan Cluster-nya saja. Hal ini dikarenakan, model data akan dijoin dengan hasil pemodelan menggunakan algoritma PCA.

Untuk melakukan filtrasi, diperlukan node **Spark Column Filter**.

Berikut adalah konfigurasi dan hasilnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/kmeans%20filter%20config.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/kmeans%20filter%20hasil.JPG?raw=true)



Setelah proses pemodelan untuk kedua algoritma selesai, maka hasil pemodelan tersebut akan di-joinkan menggunakan node **Spark Joiner**.

Berikut adalah hasilnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/spark%20joiner%20hasil%20pca%20kmeans.JPG?raw=true)

Lalu, selanjutnya data model akan diubah ke format table dan di-denormalisasi agar dapat dilakukan proses evaluasi.

Untuk mengubah data ke format table dapat menggunakan node **Spark to Table**. Dan untuk melakukan denormalisasi data, dapat menggunakan node **Denormalizer PMML**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/denormalisasi.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/denormalisasi%20hasil.JPG?raw=true)

Setelah itu, model data telah siap untuk proses Evaluasi.

## 4. Evaluation

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/evaluation1.JPG?raw=true)

Pada tahap ini, data yang telah dimodelkan menggunakan algoritma PCA dan K-Means akan di-evaluasi dengan cara di Plot menggunakan node **Scatter Plot**.

Sebelum masuk ke node **Scatter Plot**, format angka pada data harus diubah ke format string, agar proses pemberian warna pada data lebih jelas.
Proses tersebut ada pada node **Number To String** walaupun node ini telah **(deprecated/kadaluarsa)**. 

Setelah data diubah ke format string, maka data akan diberi warna menggunakan node **Color Manager**. Lalu data akan diproses menggunakan **Scatter Plot**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/scatter%20plot%20config.JPG?raw=true)

Pada konfigurasi di atas, kolom yang dipilih untuk diplot adalah PCA dimension 0 dan dimension 1.

Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/scatter%20plot%20hasil.JPG?raw=true)

Selain menggunakan **Scatter Plot**, data akan dievaluasi dengan node **Table View**. Berikut adalah hasilnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/table%20hasil.JPG?raw=true)

Proses Evaluation telah selesai.

## 6. Deployment

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/deployment1.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas7/dokum/deployment.JPG?raw=true)

Pada proses ini, data hasil dari Evaluation akan di-deploy pada HIVE dan Parquet menggunakan node **Spark to Hive** dan **Spark to Parquet**.

Untuk Spark to Hive, berikut adalah konfigurasinya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/hive%20deploy%20config.JPG?raw=true)

tabel akan dinamai **hasil_beer**. Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/hive%20deploy%20hasil.JPG?raw=true)


Jika kita cek menggunakan DBeaver, maka hasilnya adalah sebagai berikut:

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/hive%20deploy%20hasil%20dbeaver.JPG?raw=true)

Untuk Spark to Parquet, konfigurasinya adalah sebagai berikut:

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/parquet%20deploy%20config.JPG?raw=true)

Hasilnya akan disimpan pada direktori **beer_aggs**. **Spark to Parquet** berfungsi untuk mengubah format data yang awalnya adalah **DataFrame/RDD** menjadi format **file Parquet**. Berikut adalah hasilnya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/eas/Monthly%20Beer%20Production/dokum/parquet%20deploy%20hasil.JPG?raw=true)

Terima Kasih.
