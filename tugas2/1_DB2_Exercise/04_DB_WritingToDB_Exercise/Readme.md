# 04_DB_WritingToDB_Exercise
Exercise Description:
![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/04_DB_WritingToDB_Exercise/dokumentasi/description.JPG?raw=true)

Pada latihan 4 ini mirip seperti latihan yang ketiga, akan tetapi hasil dari **Decision Tree Predictor** akan di assign ke tabel **ss13pme** sebagai hasil update dari kolom **COW**.

Berikut skema lengkapnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/04_DB_WritingToDB_Exercise/dokumentasi/exercie%204.JPG?raw=true)

Ketika ada data COW is NULL yang missing (hilang), maka kita harus menjalankan workflow **Regenerate ss13me tables** agar datanya kembali lengkap. Berikut skemanya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/04_DB_WritingToDB_Exercise/dokumentasi/regen.JPG?raw=true)

Ubah nama tabel pada node **DB Table Creator** sesuai format NRP.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/04_DB_WritingToDB_Exercise/dokumentasi/db%20table%20creator%20ss13hme.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/04_DB_WritingToDB_Exercise/dokumentasi/db%20table%20creator%20ss13pme.JPG?raw=true)

Lalu jalankan workflownya.
Setelah itu, kembali lagi ke workflow latihan 4, hasil dari node **Decision Tree Learner** akan disimpan dalam tabel model dengan timestamp.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/04_DB_WritingToDB_Exercise/dokumentasi/timestamp%20and%20db%20writer.JPG?raw=true)

Hasil dari **Decision Tree Predictor** akan digunakan sebagai update dari kolom **cow** pada tabel **05111740000025_ss13pme**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/04_DB_WritingToDB_Exercise/dokumentasi/update.JPG?raw=true)

Terima Kasih.
