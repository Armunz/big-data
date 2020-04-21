# Implementasi Kafka dengan Docker

## Windows
Implementasi ini menggunakan Docker command.

### 1. Instalasi Docker Desktop
Untuk proses instalasi Docker pada Windows, bisa membuka link berikut
[https://docs.docker.com/docker-for-windows/install/](https://docs.docker.com/docker-for-windows/install/).
![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas4/dokumentasi/download%20docker%20windows.JPG?raw=true)

#### System Requirement:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas4/dokumentasi/system%20requirement%20windows.JPG?raw=true)

Jika OS Windows 10 adalah versi **Home**, maka tidak bisa memakai **Docker Desktop**.

#### Installation Guide:
![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas4/dokumentasi/installation%20guide%20windows.JPG?raw=true)

Setelah terinstall, maka kita siap untuk mengimplementasikan Kafka menggunakan Docker. Perlu digaris bawahi, **Kafka** memerlukan Java JDK versi 8 agar bisa dijalankan. Jika belum ada Java JDK, maka bisa download dan install pada link berikut: [Java](https://www.oracle.com/java/technologies/javase-downloads.html).
Anda juga bisa melihat proses instalasinya pada link berikut: [How to install java for Windows](https://www.guru99.com/install-java.html) 

### 2. Membuat Docker Network
Langkah pertama adalah membuat Docker Network untuk menampung cluster dari **Zookeper**. Ketikkan command berikut pada **Windows CMD**.

    docker network create kafka-net --driver bridge

Zookeeper berfungsi untuk menjalankan (run) **broker** dari Kafka. Kafka akan menyimpan informasi tentang **cluster** dan **customers** ke **Zookeeper**.

Ada istilah-istilah penting pada Kafka yaitu:

 - Broker: Kafka server untuk menjalankan sebuah proses. Tugasnya adalah untuk mendapatkan **message** dari producers, menyimpannya ke disk, dan merespon request dari **consumers**.
 - Producer: Fungsi dari Producer adalah untuk push data ke **Broker**. 
 - Consumer: Fungsi dari Consumer adalah untuk merequest data hasil dari **Broker**.

Berikut adalah hasilnya.
![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas4/dokumentasi/create%20docker%20network%20cluster.JPG?raw=true)
 
 
 ### 3. Membuat Zookeeper Instance
 Setelah membuat Docker Network, langkah selanjutnya adalah membuat Zookeeper Instance dengan cara run Zookeeper container melalui Bitnami Zookeeper image. Zookeeper ini nantinya akan digunakan sebagai cluster manager dari Kafka. Ketikkan Command berikut di CMD:
 

    docker run --name zookeeper-server -d -p 2181:2181 --network kafka-net -e ALLOW_ANONYMOUS_LOGIN=yes bitnami/zookeeper:latest

Secara default, Zookeeper berjalan pada port 2181. Jadi portnya akan di expose menggunakan argumen **-p** agar kita bisa mengakses Zookeeper dari aplikasi client kita. Pada command **ALLOW_ANONYMOUS_LOGIN=yes** menunjukkan bahwa bisa connect ke Zookeeper secara mudah (tanpa adanya authentication). Berikut adalah hasilnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas4/dokumentasi/run%20zookeeper%20container%20from%20bitnami%20zookeeper%20image.JPG?raw=true)

Jika sebelumnya belum ada container dari Zookeeper, maka akan otomatis mendownload instance dari Zookeeper tersebut. Setelah proses tersebut selesai, selanjutnya akan kita tes menggunakan **telnet** untuk memastikan Zookeper telah terinstall dengan baik. Ketikkan command berikut pada CMD.

    telnet localhost 2181

Berikut hasilnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas4/dokumentasi/hasil%20telnet%20zookeeper.JPG?raw=true)

### 4. **Membuat Kafka instance**

Setelah itu, kita akan membuat Kafka instance dengan cara menjalankan broker pertama kita yang akan dijoin ke cluster. Ketikkan command berikut di CMD.

    docker run --name kafka-server1 -d --network kafka-net -e ALLOW_PLAINTEXT_LISTENER=yes -e KAFKA_CFG_ZOOKEEPER_CONNECT=zookeeper-server:2181 -e KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9092 -p 9092:9092 bitnami/kafka:latest

Untuk melakukan join broker ke cluster, maka kita perlu untuk memperkenalkan broker tersebut ke Zookeeper. Caranya adalah dengan mengetahui alamat ip dari Zookeeper cluster. Maka dari itu, ada command seperti **KAFKA_CFG_ZOOKEEPER_CONNECT=zookeeper-server:2181**.
Berikut adalah hasilnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas4/dokumentasi/run%20first%20broker%20%28kafka%29%20and%20join%20to%20cluster%20%28zookeeper%29.JPG?raw=true)

Sama seperti yang tadi, jika sebelumnya belum ada broker, maka akan otomatis terdownload.

### 5. Testing Kafka Cluster dengan Conduktor
Setelah proses tadi selesai. Selanjutnya kita akan mencoba untuk membuat koneksi ke cluster kafka menggunakan Conduktor. Conduktor ini adalah sebuah GUI untuk merepresentasikan Kafka Cluster. Anda bisa mendownload dan menginstall Conduktor pada link berikut: [Conduktor](https://www.conduktor.io/download) 
Setelah proses instalasi selesai, maka buka aplikasi Conduktor tersebut. Hasilnya adalah sebagai berikut.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas4/dokumentasi/conduktor.JPG?raw=true)

Untuk melihat detail dari Kafka Broker, klik pada **BROKERS**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas4/dokumentasi/conductor%20broker.JPG?raw=true)

Ketika semuanya telah selesai, maka proses implementasi Kafka pada Docker telah selesai.

