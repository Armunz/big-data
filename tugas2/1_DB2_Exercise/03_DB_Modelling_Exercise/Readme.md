# 03_DB_Modelling_Exercise
Exercise Description:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/03_DB_Modelling_Exercise/dokumentasi/description.JPG?raw=true)

Pada Latihan kai ini, akan diimplementasikan Algoritma **Decision Tree** pada tabel **05111740000025_ss13pme**.
Untuk skema lengkapnya adalah sebagai berikut:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/03_DB_Modelling_Exercise/dokumentasi/exercise%203.JPG?raw=true)

Pertama, pilih node **SQLite Connector** untuk mengkoneksikan ke database.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/03_DB_Modelling_Exercise/dokumentasi/sql%20conn.JPG?raw=true)

Lalu konfigurasikan nodenya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/03_DB_Modelling_Exercise/dokumentasi/sql%20conn%20config.JPG?raw=true)

Lalu execute nodenya. Selanjutnya, pilih node **DB Table Selector** untuk memilih tabel yang akan digunakan. 

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/03_DB_Modelling_Exercise/dokumentasi/db%20table%20selector.JPG?raw=true)

Lalu konfigurasikan nodenya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/03_DB_Modelling_Exercise/dokumentasi/db%20table%20selector%20config.JPG?raw=true)

Pilih tabel **05111740000025_ss13pme**. Lalu execute nodenya. Setelah itu, pilih node **DB Column Filter** untuk menghapus kolom **puma** dan **pwgtp**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/03_DB_Modelling_Exercise/dokumentasi/db%20column%20filter.JPG?raw=true)

Lalu konfigurasikan nodenya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/03_DB_Modelling_Exercise/dokumentasi/db%20column%20filter%20config.JPG?raw=true)

Setelah itu, execute nodenya. Kemudian pilih node **DB Row Filter** untuk melakukan filtering **COW is NULL** dan **COW is not NULL**. Hasil dari **COW is not NULL** akan menjadi data training. Sedangkan yang **COW is NULL** akan menjadi data testing.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/03_DB_Modelling_Exercise/dokumentasi/db%20row%20filter.JPG?raw=true)
![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/03_DB_Modelling_Exercise/dokumentasi/db%20row%20filter%20null.JPG?raw=true)

Lalu konfigurasikan kedua node tadi. 

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/03_DB_Modelling_Exercise/dokumentasi/db%20row%20filter%20not%20null%20config.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/03_DB_Modelling_Exercise/dokumentasi/db%20row%20filter%20null%20config.JPG?raw=true)

Selanjutnya, execute kedua nodenya. Kemudian, hasil dari **COW is not NULL** akan di read pada node **DB Reader**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/03_DB_Modelling_Exercise/dokumentasi/db%20reader%20not%20null.JPG?raw=true)

Kemudian execute nodenya dan lihat hasilnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/03_DB_Modelling_Exercise/dokumentasi/db%20reader%20not%20null%20hasil.JPG?raw=true)

Hasil tersebut akan diubah ke dalam format string menggunakan node **Number to String**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/03_DB_Modelling_Exercise/dokumentasi/Number%20To%20String.JPG?raw=true)

Lalu konfigurasikan nodenya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/03_DB_Modelling_Exercise/dokumentasi/Number%20To%20String%20config.JPG?raw=true)

Yang akan diubah formatnya dalam bentuk string adalah kolom **COW**.
Setelah itu, execute nodenya. Proses selanjutnya adalah memasang node **Decision Tree Learner** untuk mengimplementasikan algoritma klasifikasi **Decision Tree**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/03_DB_Modelling_Exercise/dokumentasi/Decision%20tree%20learner.JPG?raw=true)

Lalu konfigurasikan nodenya.
![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/03_DB_Modelling_Exercise/dokumentasi/Decision%20tree%20learner%20config.JPG?raw=true)

Setelah itu execute nodenya.
Lalu untuk melakukan prediksi, akan digunakan node **Decision Tree Predictor**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/03_DB_Modelling_Exercise/dokumentasi/decision%20tree%20predictor.JPG?raw=true)


Decision Tree Predictor ini membutuhkan 2 input. Yang pertama dari **Decision Tree Learner**, dan yang kedua adalah dari data yang akan digunakan sebagai testing yaitu **COW is NULL**. Maka dari itu, kita membutuhkan node **DB Column Filter** untuk menghapus kolom **cow** dan node **DB Reader** untuk mendapatkan hasilnya, yang nantinya akan menjadi input dari **Decision Tree Predictor**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/03_DB_Modelling_Exercise/dokumentasi/db%20column%20filter%20remove%20cow.JPG?raw=true)

Berikut konfigurasinya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/03_DB_Modelling_Exercise/dokumentasi/db%20column%20filter%20remove%20cow%20config.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/03_DB_Modelling_Exercise/dokumentasi/db%20reader%20null.JPG?raw=true)

Berikut adalah hasil dari **DB Reader**. 

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/03_DB_Modelling_Exercise/dokumentasi/db%20reader%20null%20hasil.JPG?raw=true)


Karena isi dari **DB Reader** kosong yang menandakan tidak adanya data. Maka node **Decision Tree Predictor** tidak dapat dijalankan. Oleh karena itu, pada latihan 4 nanti, akan ada pengisian data, sehingga node tersebut bisa dijalankan.

Terima Kasih.
