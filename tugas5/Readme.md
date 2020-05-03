# Create Apache Spark Cluster Menggunakan Docker

## 2 Worker dan 2 CPU Cores

### Step 1: Membuat Apache Spark Cluster

 1.  Buat sebuah folder baru dengan nama spark.
 2. Download file docker compose dari: [https://raw.githubusercontent.com/bitnami/bitnami-docker-spark/master/docker-compose.yml](https://raw.githubusercontent.com/bitnami/bitnami-docker-spark/master/docker-compose.yml)
 3. Simpan dengan nama docker-compose.yml. Letakkan file yang telah di-download ke dalam folder spark.

Edit docker-compose.yml seperti gambar berikut.
![enter image description here](https://raw.githubusercontent.com/Armunz/big-data/master/tugas5/dokumentasi/2%20worker%202%20cpu/2%20worker%202%20cpu.png)
Untuk menggunakan 2 CPU Core, isi **SPARK_WOKER_CORES=2**.
Lalu jalankan script yml tersebut menggunakan command

    docker-compose up

Jika docker compose belum terinstall, maka kunjungi link berikut: [https://docs.docker.com/compose/install/](https://docs.docker.com/compose/install/).

![enter image description here](https://raw.githubusercontent.com/Armunz/big-data/master/tugas5/dokumentasi/2%20worker%202%20cpu/docker%20compose-up%202%20worker.png)
Ketika proses selesai, maka buka terminal baru, dan ketikkan command

    docker ps

untuk mengecek proses apa saja yang berjalan pada docker.

![enter image description here](https://raw.githubusercontent.com/Armunz/big-data/master/tugas5/dokumentasi/2%20worker%202%20cpu/docker%20ps%202%20worker.png)
Lalu, tes spark cluster menggunakan web UI di port 8080 ([http://localhost:8080](http://localhost:8080)).

![enter image description here](https://raw.githubusercontent.com/Armunz/big-data/master/tugas5/dokumentasi/2%20worker%202%20cpu/spark%202%20worker.png)

### Step 2: Menjalankan Script Python di Dalam Apache Spark Cluster

1. Buka cmd dan cek apa saja container yang sedang berjalan dengan command `docker ps`

![enter image description here](https://raw.githubusercontent.com/Armunz/big-data/master/tugas5/dokumentasi/2%20worker%202%20cpu/docker%20ps%202%20worker.png)
Perhatikan container ID dari spark_spark_1. Kita akan masuk ke dalam container tersebut dan menjalankan spark-submit.

2.  Masuklah ke dalam container dan mengeksekusi bash dengan menggunakan command `docker exec -it <container_id> /bin/bash`

![enter image description here](https://raw.githubusercontent.com/Armunz/big-data/master/tugas5/dokumentasi/2%20worker%202%20cpu/bin%20bash.png)

3.  Cek alamat IP dengan menggunakan perintah `hostname -i`.

![enter image description here](https://raw.githubusercontent.com/Armunz/big-data/master/tugas5/dokumentasi/2%20worker%202%20cpu/hostname.png)
Kita akan menggunakan alamat ip tersebut untuk melakukan submit job pada Apache Spark.

4.  Untuk melakukan submit job, Apache Spark menyediakan command spark-submit. Saat melakukan submit job ke cluster, kita perlu mendefinisikan hal-hal berikut:
    

-   Alamat master node
    
-   Script atau kelas yang akan dijalankan
    
-   Data/file (apabila ada)

#### 100 Partisi (Job)
Jalankan perintah berikut untuk submit job menghitung nilai Pi. Script contoh ini telah disediakan oleh Apache Spark.

    spark-submit --master spark://172.18.0.4:7077  examples/src/main/python/pi.py  100

Angka 100 menandakan akan menjalankan 100 partisi. 

![enter image description here](https://raw.githubusercontent.com/Armunz/big-data/master/tugas5/dokumentasi/2%20worker%202%20cpu/pi%20100%20partition%202%20worker.png)

#### 1000 Partisi (Job)

Untuk menjalankan 1000 partisi, tinggal ganti saja parameternya menjadi 1000

    spark-submit --master spark://172.18.0.4:7077  examples/src/main/python/pi.py  1000

![enter image description here](https://raw.githubusercontent.com/Armunz/big-data/master/tugas5/dokumentasi/2%20worker%202%20cpu/pi%201000%20partition%202%20worker.png)
Setelah kedua proses tersebut selesai, kita bisa mengecek laporan prosesnya di ([localhost:8080](%28http://localhost:8080%29)).

![enter image description here](https://raw.githubusercontent.com/Armunz/big-data/master/tugas5/dokumentasi/2%20worker%202%20cpu/proses%202%20worker%20100%20dan%201000%20partisi.png)
**Keterangan**:
Script pi.py adalah script yang berisi program untuk menghitung estimasi nilai pi. Dalam kasus ini, kita menggunakan parameter 100 dan 1000. Yang artinya adalah proses perhitungan/perulangan dipecah menjadi 100 bagian (100 job) dan 1000 job. Job-job inilah yang akan dijalankan oleh worker.

Untuk menjalankan 1000 partisi dibutuhkan waktu selama **1.6 menit**. Sedangkan untuk menjalankan 100 partisi hanya dibutuhkan **15 detik saja**.

Ketika semua proses telah selesai, kita bisa ketik **exit** pada terminal yang digunakan untuk menjalankan script python. Lalu tekan **Ctrl + C** pada terminal yang menjalankan Spark Worker.


## 2 Worker dan 4 CPU Cores

Langkah-langkahnya mirip seperti sebelumnya, kita hanya perlu mengganti **SPARK_WOKER_CORES=4**.

![enter image description here](https://raw.githubusercontent.com/Armunz/big-data/master/tugas5/dokumentasi/2%20worker%204%20cpu/2%20worker%204%20cpu.png)
Lalu jalankan command `docker-compose up` dan tunggu prosesnya sampai selesai. 

Setelah itu, buka terminal baru untuk masuk ke dalam container dengan command `docker exec -it <container_id> /bin/bash`.

#### 100 Partisi (Job)
Setelah itu, jalankan command berikut untuk menjalankan Spark Submit ke Spark Worker.

    spark-submit --master spark://172.18.0.2:7077  examples/src/main/python/pi.py  100

![enter image description here](https://raw.githubusercontent.com/Armunz/big-data/master/tugas5/dokumentasi/2%20worker%204%20cpu/pi%20cpu%204%20core%20100%20partisi.png)

#### 1000 Partisi (Job)

Ubah parameternya menjadi 1000.

![enter image description here](https://raw.githubusercontent.com/Armunz/big-data/master/tugas5/dokumentasi/2%20worker%204%20cpu/pi%20cpu%204%20cores%201000%20partisi.png)
Setelah itu, kita cek hasil prosesnya di ([localhost:8080](%28http://localhost:8080%29)).

![enter image description here](https://raw.githubusercontent.com/Armunz/big-data/master/tugas5/dokumentasi/2%20worker%204%20cpu/hasil%20menjalankan%20100%20dan%201000%20partisi%20dengan%20cpu%204%20core.png)
Untuk menjalankan 100 partisi, dibutuhkan waktu **15 detik**. Sedangkan untuk 1000 partisi diperlukan waktu **1.4 menit**.

## 5 Worker dan 2 CPU Cores

Langkah-langkahnya mirip seperti sebelumnya, kita hanya perlu menambah worker pada script yml.

![enter image description here](https://raw.githubusercontent.com/Armunz/big-data/master/tugas5/dokumentasi/5%20worker%202%20cpu/5%20worker%202%20cpu.png)
Lalu jalankan command `docker-compose up` dan tunggu prosesnya sampai selesai. 

Setelah itu, buka terminal baru untuk masuk ke dalam container dengan command `docker exec -it <container_id> /bin/bash`.

![enter image description here](https://raw.githubusercontent.com/Armunz/big-data/master/tugas5/dokumentasi/5%20worker%202%20cpu/5%20worker.png)
#### 100 Partisi (Job)
Setelah itu, jalankan command berikut untuk menjalankan Spark Submit ke Spark Worker.

    spark-submit --master spark://172.18.0.2:7077  examples/src/main/python/pi.py  100

![enter image description here](https://raw.githubusercontent.com/Armunz/big-data/master/tugas5/dokumentasi/5%20worker%202%20cpu/pi%205%20worker%202%20cpu%20100%20partisi.png)
#### 1000 Partisi (Job)

Ubah parameternya menjadi 1000.

![enter image description here](https://raw.githubusercontent.com/Armunz/big-data/master/tugas5/dokumentasi/5%20worker%202%20cpu/pi%205%20worker%202%20cpu%201000%20partisi.png)
Setelah itu, kita cek hasil prosesnya di ([localhost:8080](%28http://localhost:8080%29)).

![enter image description here](https://raw.githubusercontent.com/Armunz/big-data/master/tugas5/dokumentasi/5%20worker%202%20cpu/hasil%205%20worker%202%20cpu.png)
Untuk menjalankan 100 partisi, dibutuhkan waktu **23 detik**. Sedangkan untuk 1000 partisi diperlukan **1.6 menit**.

## 5 Worker dan 4 CPU Cores

Langkah-langkahnya mirip seperti sebelumnya, kita hanya perlu mengganti **SPARK_WOKER_CORES=4** pada setiap worker.

Lalu jalankan command `docker-compose up` dan tunggu prosesnya sampai selesai. 

Setelah itu, buka terminal baru untuk masuk ke dalam container dengan command `docker exec -it <container_id> /bin/bash`.

#### 100 Partisi (Job)
Setelah itu, jalankan command berikut untuk menjalankan Spark Submit ke Spark Worker.

    spark-submit --master spark://172.18.0.2:7077  examples/src/main/python/pi.py  100

![enter image description here](https://raw.githubusercontent.com/Armunz/big-data/master/tugas5/dokumentasi/5%20worker%204%20cpu/pi%205%20worker%204%20cpu%20100%20partisi.png)

#### 1000 Partisi (Job)

Ubah parameternya menjadi 1000.

![enter image description here](https://raw.githubusercontent.com/Armunz/big-data/master/tugas5/dokumentasi/5%20worker%204%20cpu/pi%205%20worker%204%20cpu%201000%20partisi.png)
Setelah itu, kita cek hasil prosesnya di ([localhost:8080](%28http://localhost:8080%29)).

![enter image description here](https://raw.githubusercontent.com/Armunz/big-data/master/tugas5/dokumentasi/5%20worker%204%20cpu/hasil%205%20worker%204%20cpu.png)
Untuk menjalankan 100 partisi, dibutuhkan waktu **22 detik**. Sedangkan untuk 1000 partisi diperlukan waktu **1.6 menit**.

## Kesimpulan
Semakin banyak Job (Partisi) yang digunakan, maka proses overhead dari map-reduce akan semakin lama, apalagi jika jumlah workernya semakin banyak. Akan tetapi, ketika jumlah workernya diperbanyak dan pada setiap worker diberikan CPU Cores lebih banyak, maka proses komputasinya menjadi semakin tinggi, ini ditunjukkan dengan termakannya jumlah CPU dan RAM yang lebih besar ketika saya menjalankan 5 worker.

