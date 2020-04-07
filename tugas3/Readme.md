# Implementasi Algoritma ALS (Alternating Least Square) untuk mendapatkan Rekomendasi Film dari rating yang diberikan oleh user menggunakan Apache Spark Collaborative Filtering di KNIME.

## Overview

Berikut adalah workflow KNIME-nya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas3/dokumentasi/overview.png?raw=true)

## Business Understanding
Tujuan bisnisnya adalah untuk menampilkan rekomendasi film kepada user berdasarkan hasil rating beberapa film yang diberikan oleh user tersebut. Metode yang digunakan adalah Spark Collaborative Filtering yang menggunakan Algoritma Alternating Least Square untuk faktorisasi matriks agar bisa mempelajari latent factornya.

Cara kerjanya adalah meminta user untuk melakukan rating pada 20 film yang random yang nantinya data tersebut akan digunakan sebagai sample dari user tersebut agar user tersebut mendapatkan rekomendasi 20 film.

## Data Understanding

Ada 2 dataset yang akan digunakan, yaitu dataset film dan rating.
Kedua dataset tersebut sudah tersedia di workflow KNIME, namun jika ingin mengunduhnya bisa di link berikut: [Movielens 20M Dataset](https://grouplens.org/datasets/movielens/)
Pada dataset film **(movies.csv)** terdapat 3 kolom dengan data sebanyak **131262** (130 ribu) data. Kolomnya adalah sebagai berikut:

 - movieId 	: berisi id dari film
 - title			: berisi judul film
 - genres : berisi genre film

Sedangkan pada dataset rating **(ratings.csv)** terdapat 4 kolom dengan data sebanyak **20000263** (2 juta) data. Kolomnya adalah sebagai berikut:

 - userId : berisi id user yang memberikan rating sebuah film
 - movieId : berisi id dari film yang sedang diberikan rating oleh user
 - rating : berisi besar rating yang diberikan oleh user
 - timestamp : berisi waktu kapan user tersebut memberikan rating pada sebuah film

## Data Preparation

Untuk mempersiapkan dataset yang akan diproses, maka kita load dataset **movie.csv** dan **rating.csv**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas3/dokumentasi/Data%20Preparation%20%28Build%20Current%20User%20Profile%29.JPG?raw=true)

Untuk dataset **movie.csv** akan diload menggunakan Node **File Reader**. Pilih dataset **movie.csv** yang sudah tersedia pada workflownya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas3/dokumentasi/movies.csv%20local.JPG?raw=true)

Untuk node selanjutnya adalah **add fields** dimana itu digunakan untuk menambahkan data field **timestamp = 123** dan **userID = 999999** yang menunjukkan bahwa ada user baru yang akan melakukan rating pada film (yaitu diri kita sendiri).

Selanjutnya, proses akan menuju ke node **Row Splitter** untuk memisahkan 20 film yang akan diberikan rating oleh user baru. Untuk film sisanya akan dijadikan sebagai **data testing** dengan menggunakan node **no ratings**.  
**NOTE : Secara default, semua film yang ada pada dataset movies.csv belum memiliki rating**

Lalu, proses selanjutnya adalah melakukan rating pada 20 film random yang telah dipisahkan tadi akan diberikan rating pada node **Ask User for Movie Ratings**. 

Keterangan:
 - **0** untuk film yang tidak akan pernah dilihat lagi
 - **5** untuk film yang bagus
 - **-1** untuk film yang belum ditonton

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas3/dokumentasi/user%20input%20rating%2020%20film.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas3/dokumentasi/user%20input%20rating%2020%20film%20%282%29.JPG?raw=true)

Kemudian, untuk dataset **ratings.csv** akan diload menggunakan node **CSV to Spark**. Mengapa menggunakan node tersebut? Nanti akan dibahas dibawah.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas3/dokumentasi/csv%20to%20spark.JPG?raw=true)

Untuk mengaktifkan node tersebut, dibutuhkan **Spark Environment**, jadi akan digunakan juga node **Create Local Big Data Environment**. 

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas3/dokumentasi/rating.csv%20local.JPG?raw=true)

Pilih dataset **ratings.csv**. Disini, saya menggunakan dataset yang telah di unduh pada link di atas tadi. 

## Modelling

Kemudian, masuk ke tahap **modelling**, dimana data yang sudah dipersiapkan akan diubah formatnya sesuai dengan metode evaluasi yang akan dipakai, yaitu **Apache Spark Collaborative Filtering**.

Data movies yang telah dipecah tadi, diubah formatnya kedalam **Spark** agar dapat dijalankan dengan algoritma **Spark Collaborative Filtering**. Proses pemodelan data tersebut menggunakan node **Table to Spark**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas3/dokumentasi/modelling.JPG?raw=true)

Sedangkan untuk data rating akan dipecah menjadi **80%-20%**. Dimana yang 80% akan digunakan sebagai data training pada **Spark Collaborative Filtering**. 
Lalu yang 20% akan digunakan untuk data testing untuk mengecek persentase error yang terjadi.
Untuk memecah data tersebut, digunakan node **Spark Partitioning**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas3/dokumentasi/modelling%202.JPG?raw=true)


## Evaluation

Lalu, proses selanjutnya adalah evaluation, dimana dalam proses ini Algoritma Spark Collaborative Filtering akan diterapkan.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas3/dokumentasi/evaluation.JPG?raw=true)

Data rating yang telah dipartisi sebesar 20% tadi akan ditambah dengan data 20 film yang telah diberikan rating oleh user. Untuk menggabungkan kedua data tersebut, digunakan node **Spark Concatenate**. Berikut hasilnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas3/dokumentasi/hasil%20spark%20concatenate.JPG?raw=true)

Berdasarkan hasil tersebut, masih belum terlihat data dari **userID 999999**. Itu dikarenakan data tersebut terletak pada bagian terbawah dari keseluruhan data.

Ketika kedua data tadi sudah berhasil untuk digabung (concatenate), maka akan diproses di node **Spark Collaborative Filtering**. Disinilah Algoritma **ALS (Alternating Least Square)** berjalan.

Setelah proses dari node tersebut selesai, maka hasilnya adalah model rekomendasi rating yang telah diprediksi pada keseluruhan data yang menjadi inputnya. 

Untuk proses selanjutnya adalah **Testing** untuk mengevaluasi kemungkinan error yang terjadi. Untuk melakukannya, diperlukan node **Spark Predictor** yang akan mengenerate kolom **Prediction** yang berisi prediksi rating dari film-film yang ada pada data input. 

Setelah tergenerate, proses selanjutnya adalah untuk menghapus data dengan value **NaN** yang akan dilakukan oleh node **Spark Missing Value**. Setelah semua data **NaN** terhapus, maka data akan dihitung errornya berdasarkan rating sesungguhnya (original rating) menggunakan node **Spark Numeric Scorer**.  Berikut adalah hasilnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas3/dokumentasi/scorer.JPG?raw=true)

 Pada hasil tersebut menunjukkan bahwa nilai dari **Mean absolute erronya adalah 0.615** dimana pada dasarnya penyimpangan prediksi dari rating memang diantara +/- 0.6.

## Deployment

Selanjutnya adalah tahap **Deployment**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas3/dokumentasi/deployment.JPG?raw=true)

Pada tahap ini, akan ditampilkan 20 film yang akan direkomendasikan pada user yang telah memberikan rating pada 20 film random sebelumnya.
Tahap pertama yaitu node **Spark Predictor** yang akan membuat table Prediction dengan data prediksi rating film. Node ini mendaptkan input dari node **Spark Collaborative Filtering** dan **Table to Spark** yang berisi film-film yang belum dilakukan rating oleh si user. 

Lalu hasil dari **Spark  Predictor** akan diubah formatnya kembali ke dalam bentuk tabel karena proses **Apache Spark** telah selesai. Langkah selanjutnya adalah melakukan rekomendasi 20 film terbaik dari hasil prediksi rating oleh node **Spark Predictor**. 

Pada node **Top 20 recommendation movies** didalamnya terdapat node-node seperti berikut.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas3/dokumentasi/top%2020.JPG?raw=true)

Hasil dari node **Spark to Table** akan masuk ke node **Row Filter** untuk menghapus data dengan value **Nan**. Setelah itu, hasilnya akan masuk ke node **Sorter** untuk melakukan sorting (mengurutkan) 20 film berdasarkan prediksi rating yang terbaik. Setelah itu, ada node **File Reader** yang digunakan untuk load dataset movie.csv yang nantinya digunakan untuk mendapatkan Judul Film beserta genrenya. Setelah itu, kedua node tadi akan dijoin menggunakan node **Joiner** untuk mendapatkan hasilnya yaitu **20 Film Rekomendasi**.

Selanjutnya, hasil dari node **Joiner** tadi akan ditampilkan menggunakan node **Display Recommendations**. Berikut ini hasilnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas3/dokumentasi/hasil%20rekomendasi%20film.JPG?raw=true)

Hasil rekomendasi 20 film tadi juga disimpan dalam file csv dengan node **CSV Writer**. Berikut hasil dari CSV-nya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas3/dokumentasi/hasil%20rekomendasi%20film%20csv.JPG?raw=true)

## File Reader vs CSV to Spark

Nah pada bagian ini akan dibahas mengapa lebih memilih node **CSV to Spark** untuk load dataset **ratings.csv**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas3/dokumentasi/perbandingan%20csv%20to%20spark%20dan%20file%20reader.JPG?raw=true)

Jika kita amati dari **Execution time** dari **File Reader** dan **CSV to Spark** saat load dataset **ratings.csv** dengan menggunakan node **Timer Info**, disitu terlihat bahwa Waktu eksekusi dari CSV to Spark adalah 65096 milisecond atau **65 detik**. Sedangkan untuk **File Reader**, waktu eksekusinya yaitu 142130 milisecond atau **142 detik**.

|  File Reader| CSV to Spark  |
|--|--|
| 142 detik| 65 detik|


**Apache Spark** memang sengaja dirancang untuk menangani data yang jumlahnya besar. Pada kasus ini datanya adalah **rating.csv** yang memiliki 2 juta data. Mekanismenya sendiri yaitu Spark tidak membaca data dalam bentuk table, akan tetapi dalam bentuk **Frame**. Karena bentuk datanya **Frame**, maka pemrosesan data dapat dilakukan secara paralel. Maka dari itu, **CSV to Spark** lebih dipilih daripada **File Reader**.

Akan tetapi, jika **CSV to Spark** digunakan untuk pemrosesan data yang kecil seperti dataset **movies.csv**, maka mungkin waktu eksekusinya akan kalah dengan **File Reader**.
