# 00_Setup_Hive_Table
Exercise Description:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/2_Hadoop_Exercise/00_Setup_Hive_Table/dokumentasi/description.JPG?raw=true)

Pada exercise kali ini, disuruh untuk mengeksekusi skema yang telah ada di workflow tersebut. Berikut ini skemanya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/2_Hadoop_Exercise/00_Setup_Hive_Table/dokumentasi/exercise%201.JPG?raw=true)

Node yang pertama yaitu **Create Temp Dir** digunakan untuk membuat direktori temporary (sementara). 

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/2_Hadoop_Exercise/00_Setup_Hive_Table/dokumentasi/create%20temp%20dir.JPG?raw=true)

Berikut konfigurasinya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/2_Hadoop_Exercise/00_Setup_Hive_Table/dokumentasi/create%20temp%20dir%20config.JPG?raw=true)

Node selanjutnya yaitu **String Manipulation (Variable)** yang digunakan untuk membuat path yang kompatibel dengan tipe file **HDFS**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/2_Hadoop_Exercise/00_Setup_Hive_Table/dokumentasi/string%20manipulation.JPG?raw=true)

Berikut konfigurasinya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/2_Hadoop_Exercise/00_Setup_Hive_Table/dokumentasi/string%20manipulation%20config.JPG?raw=true)

Node selanjutnya yaitu **Create Local Big Data Environment** yang merupakan Ekstensi KNIME untuk Big Data. Node ini digunakan untuk membuat environment big data secara lokal (pada disk).

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/2_Hadoop_Exercise/00_Setup_Hive_Table/dokumentasi/create%20local%20big%20data%20env.JPG?raw=true)

Berikut konfigurasinya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/2_Hadoop_Exercise/00_Setup_Hive_Table/dokumentasi/create%20local%20big%20data%20env%20config.JPG?raw=true)

Pilih **HiveQl and provide JDBC connection** agar nantinya bisa terkoneksi dengan **DBeaver**.

Node selanjutnya yaitu **Table Reader** yang digunakan untuk membaca tabel **ss13hme** dan **ss13pme**.
![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/2_Hadoop_Exercise/00_Setup_Hive_Table/dokumentasi/table%20reader%20ss13hme.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/2_Hadoop_Exercise/00_Setup_Hive_Table/dokumentasi/table%20reader%20ss13pme.JPG?raw=true)

Berikut konfigurasi nodenya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/2_Hadoop_Exercise/00_Setup_Hive_Table/dokumentasi/table%20reader%20ss13hme%20config.JPG?raw=true)

Node selanjutnya yaitu **DB Table Creator** yang berfungsi untuk membuat tabel baru di hive dengan format nama NRP.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/2_Hadoop_Exercise/00_Setup_Hive_Table/dokumentasi/db%20table%20creator%20ss13hme.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/2_Hadoop_Exercise/00_Setup_Hive_Table/dokumentasi/create%20table%20ss13hme.JPG?raw=true)

Berikut konfigurasi nodenya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/2_Hadoop_Exercise/00_Setup_Hive_Table/dokumentasi/db%20table%20creator%20ss13hme%20config.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/2_Hadoop_Exercise/00_Setup_Hive_Table/dokumentasi/db%20table%20creator%20ss13pme%20config.JPG?raw=true)

Node selanjutnya yaitu **DB Loader** yang berfungsi untuk load tabel ke database hive.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/2_Hadoop_Exercise/00_Setup_Hive_Table/dokumentasi/db%20loader%20ss13pme.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/2_Hadoop_Exercise/00_Setup_Hive_Table/dokumentasi/db%20loader%20ss13hme.JPG?raw=true)

Untuk mengecek hasil dari load, bisa dilakukan filter 100 baris pertama. Berikut ini hasilnya.
![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/2_Hadoop_Exercise/00_Setup_Hive_Table/dokumentasi/hasil%20table%202.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/2_Hadoop_Exercise/00_Setup_Hive_Table/dokumentasi/hasil%20table%201.JPG?raw=true)

Untuk mengeceknya di **DBeaver**, kita perlu tahu alamat dari database hive. Alamatnya bisa dilihat pada menu db session.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/2_Hadoop_Exercise/00_Setup_Hive_Table/dokumentasi/db%20session.JPG?raw=true)

Lalu, kita cocokkan dengan yang ada di **DBeaver**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/2_Hadoop_Exercise/00_Setup_Hive_Table/dokumentasi/dbeaver%20hive%20connection.JPG?raw=true)

Setelah terkoneksi, maka kita bisa cek isi tabel dengan query limit 100.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/2_Hadoop_Exercise/00_Setup_Hive_Table/dokumentasi/cek%20dbeaver%20hme.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/2_Hadoop_Exercise/00_Setup_Hive_Table/dokumentasi/cek%20dengan%20dbeaver.JPG?raw=true)

Terima Kasih.

