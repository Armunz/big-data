# 02_DB_InDB_Processing_Exercise
Exercise Description:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/02_DB_InDB_Processing_Exercise/dokumentasi/exercise%20desc.JPG?raw=true)


Pertama, pilih node **SQLITE Connector** untuk mengkoneksikan ke database.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/02_DB_InDB_Processing_Exercise/dokumentasi/SQLite%20Connector.JPG?raw=true)

Lalu konfigurasikan nodenya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/02_DB_InDB_Processing_Exercise/dokumentasi/SQLite%20Config.JPG?raw=true)

Pilih database sesuai ketentuan dari soal latihan. Setelah itu execute nodenya.

Setelah itu, pilih node **DB Table Selector** untuk memilih tabel database yang akan diproses. DIsini akan digunakan 2 node, yang pertama untuk memilih tabel **05111740000025_ss13hme** daan yang kedua untuk tabel **05111740000025_ss13pme**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/02_DB_InDB_Processing_Exercise/dokumentasi/DB%20Table%20Selector.JPG?raw=true)
![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/02_DB_InDB_Processing_Exercise/dokumentasi/DB%20Table%20Selector%20ss13pme.JPG?raw=true)

Lalu, konfigurasikan masing-masing nodenya agar bisa memilih tabel yang dibutuhkan.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/02_DB_InDB_Processing_Exercise/dokumentasi/DB%20Table%20Selector%20Config%20ss13hme.JPG?raw=true)
![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/02_DB_InDB_Processing_Exercise/dokumentasi/DB%20Table%20Selector%20Config%20ss13pme.JPG?raw=true)

Setelah itu, execute nodenya.

## Latihan no 1
Pada soal latihan nomor 1, disuruh melakukan join kolom **serialno** pada tabel **ss13hme** dan **ss13pme**. Lalu hapus kolom yang berawalan dengan nama **puma** dan **pwgtp**.

Pertama, pilih node **DB Joiner** untuk menggabungkan kolom **serialno** pada tabel **ss13hme** dan **ss13pme**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/02_DB_InDB_Processing_Exercise/dokumentasi/DB%20Joiner.JPG?raw=true)

Lalu konfigurasikan nodenya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/02_DB_InDB_Processing_Exercise/dokumentasi/db%20join%20config.JPG?raw=true)

masukkan nama kolom pada field yang tersedia. Setelah itu, execute nodenya.

Kemudian, pilih node **DB Column Filter** untuk menghapus kolom yang berawalan **puma** dan **pwgtp**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/02_DB_InDB_Processing_Exercise/dokumentasi/DB%20Column%20Filter.JPG?raw=true)

Lalu konfigurasikan nodenya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/02_DB_InDB_Processing_Exercise/dokumentasi/puma%20filter.JPG?raw=true)

masukkan keyword **puma** pada field **Include**, lalu pilih yang berawalan dengan kata **puma** dan pilih tombol **<**. Setelah itu masukkan keyword **pwgtp**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/02_DB_InDB_Processing_Exercise/dokumentasi/pwgtp%20filter.JPG?raw=true)

Lalu pilih yang berawalan kata **pwgtp**. Setelah itu pilih tombol **<**.
Setelah itu, execute nodenya.

Kemudian, pilih node **DB Reader** untuk melihat hasilnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/02_DB_InDB_Processing_Exercise/dokumentasi/DB%20reader%20tabel%201.JPG?raw=true)

Setelah itu, execute nodenya dan lihat hasilnya dengan cara klik kanan dan pilih **KNIME Data Table**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/02_DB_InDB_Processing_Exercise/dokumentasi/hasil%20soal1.JPG?raw=true)

## Latihan no 2
Pada soal latihan nomor 2, disuruh filter semua row pada tabel **ss13pme** dimana **COW is NULL**.

Pertama, pilih node **DB Row Filter** untuk melakukan filter **COW is NULL**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/02_DB_InDB_Processing_Exercise/dokumentasi/Db%20row%20filter%20cow=null.JPG?raw=true)

Lalu konfigurasikan nodenya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/02_DB_InDB_Processing_Exercise/dokumentasi/cow%20is%20null%20row%20filter.JPG?raw=true)

Pilih kolom **cow** dan konfigurasikan **IS NULL**. Setelah itu, execute nodenya.

Untuk melihat hasilnya, maka pilihlah node **DB Reader**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/02_DB_InDB_Processing_Exercise/dokumentasi/db%20reader%20tabel%202.JPG?raw=true)

Execute nodenya dan lihat hasilnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/02_DB_InDB_Processing_Exercise/dokumentasi/hasil%20cow%20is%20null.JPG?raw=true)

Hasilnya kosong, berarti tidak ada data ketika **COW is NULL**.

## Latihan no 3
Untuk filter yang **COW is not NULL** mirip dengan yang **COW is NULL**. Hanya mengganti konfigurasinya saja menjadi **NOT NULL**.

Untuk hasil dari filter **COW is not NULL** adalah sebagai berikut.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/02_DB_InDB_Processing_Exercise/dokumentasi/hasil%20cow%20is%20not%20null.JPG?raw=true)

## Latihan no 4
Pada soal ini, disuruh menghitung rata-rata **(average)** dari **AGEP** pada masing-masing **group** jenis kelamin **(sex)**.

Pertama, pilih node **DB GroupBy** untuk mengelompokkan data berdasarkan jenis kelamin. 

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/02_DB_InDB_Processing_Exercise/dokumentasi/db%20group%20by.JPG?raw=true)

Lalu konfigurasikan nodenya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/02_DB_InDB_Processing_Exercise/dokumentasi/konfig%20group%20by%200.JPG?raw=true)

Pilih kolom **sex** yang nantinya dikelompokkan. Setelah itu, pindah ke menu **Manual Aggregation**. Lalu pilih kolom yang akan dihitung, yaitu **agep**. Setelah itu pilih metode penghitungannya, yaitu **AVG**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/02_DB_InDB_Processing_Exercise/dokumentasi/konfig%20group%20by%201.JPG?raw=true)

Setelah itu, execute nodenya. Untuk melihat hasilnya, maka pilih node **DB Reader**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/02_DB_InDB_Processing_Exercise/dokumentasi/db%20reader%20tabel%204.JPG?raw=true)

Execute nodenya dan lihat hasilnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/02_DB_InDB_Processing_Exercise/dokumentasi/hasil%20average%20age.JPG?raw=true)

Untuk skema lengkapnya adalah sebagai berikut.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/02_DB_InDB_Processing_Exercise/dokumentasi/exercise%202.JPG?raw=true)

Selesai.
Terima Kasih.

