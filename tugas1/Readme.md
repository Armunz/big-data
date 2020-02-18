# Business Understanding




# Data Understanding

Dataset yang saya gunakan berjumlah 9564 baris yang terdiri dari 50 kolom.
- rowid : Menunjukkan id dari tiap field baris data.
- kepid : Identitas dari Kepler Input Catalog (KIC).
- kepoi_name : Angka yang digunakan untuk mengidentifikasi dan melacak sebuah Kepler Object Interest (KOI).
- kepler_name : Format yang digunakan untuk mengidentifikasi planet.
- koi_disposition : kategori yang terdiri dari CANDIDATE, FALSE POSITIVE, NOT DISPOSITIONED atau CONFIRMED.
- koi_pdisposition : Sebuah pipeline flag yang didesain untuk penjelasan fisik yang mungkin dari KOI
- koi_score : Nilai antara 0 dan 1 yang mengidikasikan bukti pada KOI disposition
- koi_fpflag_nt : Sebuah KOI yang lengkungan cahanyanya tidak konsisten
- koi_fpflag_ss : Sebuah KOI yang memiliki secondary event yang signifikan
- koi_fpflag_co : Sumber sinyal dari bintang yang terdekat
- koi_fpflag_ec : Sebuah KOI yang membagikan periode dan epoch pada objek yang lain
- koi_period : Interval antara planet-planet yang transit secara berturut-turut
- koi_period_err1 : Interval error1
- koi_period_err2 : Interval error2
- koi_time0bk : Waktu yang sesuai dengan pusat dari transit pertama yang terdeteksi pada satuan Barycentric Julian Day (BJD) yang dikurangi dengan 2,454,833.0 days. 
- koi_time0bk_err1 : Error1 pada koi_time0bk
- koi_time0bk_err2 : Error2 pada koi_time0bk
- koi_impact : Sebuah jarak proyeksi langit antara pusat dari stellar disc dan pusat dari planet disc.
- koi_impact_err1 : Error1 pada koi_impact
- koi_impact_err2 : Error2 pada koi_impact
- koi_duration : Durasi dari transit yang telah terobservasi
- koi_duration_err1 : Error1 dari koi_duration
- koi_duration_err2 : Error2 dari koi_duration
- koi_depth : Fraksi star flux yang hilang pada saat planet transit minimum
- koi_depth_err1 : Error1 pada koi_depth
- koi_depth_err2 : Error2 pada koi_depth
- koi_prad : Radius dari planet
- koi_prad_err1 : Error1 pada koi_prad
- koi_prad_err2 : Error2 pada koi_prad
- koi_teq : Perkiraan suhu planet
- koi_teq_err1 : Error1 pada koi_teq
- koi_teq_err2 : Error2 pada koi_teq
- koi_insol : Suhu kesetimbangan menggunakan metode Insolation Flux
- koi_insol_err1 : Error1 pada kol_insol
- koi_insol_err2 : Error2 pada kol_insol
- koi_model_snr : Kedalaman transit
- koi_tce_plnt_num : TCE Planet Number yang terfederasi ke KOI. 
- koi_tce_delivname : TCE delivery name yang sesuai dengan KOI.
- koi_steff : Temperatur photosperic dari bintang
- koi_steff_err1 : Error1 dari koi_steff
- koi_steff_err2 : Error2 dari koi_steff
- koi_slogg : Logaritma basis-10 dari percepatan gravitasi pada permukaan bintang
- koi_slogg_err1 : Error1 pada koi_slogg
- koi_slogg_err2 : Error2 pada koi_slogg
- koi_srad : Radius photosperic dari bintang
- koi_srad_err1 : Error1 pada koi_srad
- koi_srad_err2 : Error2 pada koi_srad
- ra : KIC Right Ascension
- dec : KIC Declination
- koi_kepmag : Kepler-band (mag) 

## Data Preparation
Pada proses ini, dataset akan dibaca dan dipecah (**split**) menjadi 2 bagian.
![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas1/dokumentasi/Data%20Preparation.JPG?raw=true)
Langkah pertama adalah menggunakan tools **CSV Reader** untuk membaca dataset.
![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas1/dokumentasi/CSV%20Reader.JPG?raw=true)
Lalu lakukan konfigurasi toolsnya menjadi seperti berikut.
![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas1/dokumentasi/konfigurasi_csv_header.JPG?raw=true)
Setelah itu, drag tools lain yang bernama **Column Splitter**
![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas1/dokumentasi/Column_Splitter.JPG?raw=true)
dan konfigurasikan toolsnya seperti berikut.
![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas1/dokumentasi/Konfigurasi_Column_Splitter.JPG?raw=true)
Disini, saya membagi 50 kolom menjadi 2, 25 kolom pertama yang akan dikeluarkan di arah panah **Top** dan 25 kolom selanjutnya pada arah panah **Bottom**.

Setelah itu, pilih tools yang bernama **CSV Writer**
![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas1/dokumentasi/CSV_Writter.JPG?raw=true)
Konfigurasikan tools tersebut seperti berikut.
![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas1/dokumentasi/Konfigurasi_CSV_Writter.JPG?raw=true)**CSV Writer** yang pertama saya gunakan untuk menampung hasil dari **Column Splitter** yang **Top**. Dan **CSV Writter** yang kedua untuk menampung hasil dari **Column Splitter** yang **Bottom**
![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas1/dokumentasi/Konfigurasi_CSV_Writter2.JPG?raw=true)
Setelah itu, sambungkan panahnya sesuai dengan grafik yang di **atas**. 
Proses **Data Preparation** telah selesai.


## Modelling

Pada proses ini, salah satu data yang telah di split tadi akan diubah formatnya menjadi format **database**. Setelah itu, kedua data akan dibaca kembali. 
![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas1/dokumentasi/Modelling.JPG?raw=true)
Saya menggunakan data yang kedua untuk diubah formatnya dalam bentuk database. 

Langkah pertama adalah menggunakan tools **CSV Reader** untuk membaca data split yang pertama.
![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas1/dokumentasi/CSV_Reader_Split1.JPG?raw=true)
 Selanjutnya adalah melakukan konfigurasi pada **CSV Reader** seperti berikut.
 ![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas1/dokumentasi/Konfigurasi_CSV_Reader_Split1.JPG?raw=true)
 Cara yang sama juga digunakan untuk membaca data split yang kedua.
 ![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas1/dokumentasi/CSV_Reader_Split2.JPG?raw=true)
![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas1/dokumentasi/Konfigurasi_CSV_Reader_Split2.JPG?raw=true)
 Setelah itu, pilih tools **MySQL Connector** untuk membuat koneksi ke database.
 ![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas1/dokumentasi/MYSQL_Conn_Split2.JPG?raw=true)
 Lalu konfigurasikan tools tersebut seperti berikut. 
 ![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas1/dokumentasi/Konfigurasi_MYSQL_Conn_Split2.JPG?raw=true)
 Isikan **Hostname**, **Database Name**, serta pilih **Username & Password** untuk Authentication.
 
 Untuk proses selanjutnya, pilih tools **DB Writer** untuk memasukkan data ke database.
 ![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas1/dokumentasi/DB_Writer.JPG?raw=true)
 Lalu konfigurasikan tools tersebut seperti berikut.
 ![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas1/dokumentasi/Konfigurasi_DB_Writer.JPG?raw=true)
Setelah semuanya selesai, sambungkan **panah** sesuai dengan gambar yang di atas.


