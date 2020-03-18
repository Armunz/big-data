# 02_Hive_WritingToDB
Exercise Description:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/2_Hadoop_Exercise/02_Hive_WritingToDB/dokumentasi/description.JPG?raw=true)

Pada exercise ini mirip dengan **SQLite WritingToDB**. Akan tetapi ini menggunakan **Hive Apache Spark**. Berikut adalah skemanya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/2_Hadoop_Exercise/02_Hive_WritingToDB/dokumentasi/schema.JPG?raw=true)

Node **SQLite Connector** diganti dengan node **Create Local Big Data Environment** untuk membuat environment Big Data pada disk.
Untuk konfigurasi node-node selanjutnya mirip seperti workflow di SQLite.
Untuk menyimpan tabel hasil prediksi, maka perlu node **DB Table Creator**.
Berikut ini konfigurasinya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/2_Hadoop_Exercise/02_Hive_WritingToDB/dokumentasi/db%20table%20creator%20config.JPG?raw=true)

Beri nama tabel dengan format NRP. Untuk load tabel tersebut ke database **Hive** maka diperlukan node **DB Loader**. Berikut ini konfigurasi beserta hasil dari 100 baris pertama.
![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/2_Hadoop_Exercise/02_Hive_WritingToDB/dokumentasi/hasil%20new%20table.JPG?raw=true)

Untuk mengeceknya melalui **DBeaver**, maka akan kita query ke localhost dari **HIVE**. Berikut ini hasilnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/2_Hadoop_Exercise/02_Hive_WritingToDB/dokumentasi/hasil%20dbeaver.JPG?raw=true)

Terima Kasih.

