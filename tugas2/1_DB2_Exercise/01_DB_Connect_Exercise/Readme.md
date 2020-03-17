# 01_DB_Connect_Exercise
Exercise Description:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/01_DB_Connect_Exercise/dokumentasi/exercise%20description.JPG?raw=true)

Pertama-tama, pilih node **SQLite Connector** untuk membuat koneksi ke database SQLite.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/01_DB_Connect_Exercise/dokumentasi/sqlite%20connector.JPG?raw=true)

Lalu, konfigurasikan Connectornya pada file database yang akan dikoneksikan.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/01_DB_Connect_Exercise/dokumentasi/konfigurasi%20sqlite%20connector.JPG?raw=true)

Pada exercise, disuruh untuk mengkoneksikan ke database **newCencus.sqlite**. Setelah path ditemukan, makan tekan **OK**. Lalu **Execute** node **SQLite Connector**.

Setelah itu, pilih node **DB Table Selector** untuk memilih tabel, yang nantinya ditampilkan data dalam tabel tersebut.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/01_DB_Connect_Exercise/dokumentasi/db%20selector.JPG?raw=true)

Lalu konfigurasikan node tersebut. 

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/01_DB_Connect_Exercise/dokumentasi/konfigurasi%20pada%20db%20table%20selector.JPG?raw=true)
Pilih tabel **05111740000025_ss13pme**. Setelah itu execute node tersebut.

Setelah itu pilih node node **DB Reader** untuk menampilkan data dari node **DB Table Selector**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/01_DB_Connect_Exercise/dokumentasi/db%20reader.JPG?raw=true)

Lalu execute node tersebut dan lihat hasilnya denga klik kanan nodenya, lalu pilih **KNIME Data Table**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/01_DB_Connect_Exercise/dokumentasi/hasil%20tabel%20db%20reader.JPG?raw=true)

Selesai. Untuk skema lengkapnya seperti ini.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas2/1_DB2_Exercise/01_DB_Connect_Exercise/dokumentasi/Exercise%201.JPG?raw=true)

Terima Kasih.
