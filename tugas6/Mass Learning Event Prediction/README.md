# Implementasi Mass Learning Event Prediction dengan Spark

Berikut adalah skema KNIME workflow-nya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas6/Mass%20Learning%20Event%20Prediction/dokum/workflow.JPG?raw=true)

## 1. Business Understanding
Pada kasus ini, ketika ada data iris baru, kita bisa melakukan prediksi class dengan cara Fast Event Prediction pada data iris tersebut.

## 2. Data Understanding
Dataset yang digunakan memiliki beberapa fitur, diantaranya:

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas6/Mass%20Learning%20Event%20Prediction/dokum/iris_petal_sepal.png?raw=true)

 1. **sepal length**: panjang dari sepal bunga iris.
 2. **sepal width**: lebar dari sepal bunga iris.
 3. **petal length**: lebar petal dari bunga iris.
 4. **petal width**: lebar petal dari bunga iris.
 5. **class**: jenis kelas dari bunga iris, yaitu Iris-sentosa, Iris-versicolor, dan Iris-virginica.

## 3. Data Preparation
Data yang akan digunakan adalah dataset iris sebagai data traning dan data testing.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas6/Mass%20Learning%20Event%20Prediction/dokum/data%20preparation.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas6/Mass%20Learning%20Event%20Prediction/dokum/dataset%20iris.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas6/Mass%20Learning%20Event%20Prediction/dokum/data%20preparation%20%282%29.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas6/Mass%20Learning%20Event%20Prediction/dokum/data%20tes%20iris.JPG?raw=true)

## 4. Modelling
Pada tahap ini, kita akan memodelkan data training dari iris tersebut ke dalam bentuk **PMML** (Predictive Model Markup Language). PMML adalah bentuk standard untuk merepresentasikan sebuah **predictive solutions**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas6/Mass%20Learning%20Event%20Prediction/dokum/modelling.JPG?raw=true)

Karena prosesnya menggunakan Spark, yang pertama adalah membuat sebua Environment Big Data Local menggunakan node **Create Local Big Data Environment**. Setelah itu, kita perlu mengubah dataset tersebut ke dalam Spark dengan node **Table to Spark**. 

Lalu, data akan di training menggunakan algoritma **k-Means** pada node **Spark k-Means**. Berikut konfigurasinya. Disini k yang digunakan sejumlah 3 (sesuai dengan jumlah class dari iris) dan iterasinya sebanyak 30 kali.

Algoritma ini nantinya akan membentuk tiga cluster besar (sesuai dengan nilai k).

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas6/Mass%20Learning%20Event%20Prediction/dokum/spark%20k-means.JPG?raw=true)

Lalu, hasil dari algoritma tersebut akan diubah ke bentuk PMML menggunakan node **Spark MLib to PMML**. Agar data dalam bentuk PMML tadi dapat di compile, maka akan dikonversi ke **Java** menggunakan node **PMML Compiler**. 

Hasil dari node **PMML Compiler** nantinya akan digunakan sebagai **Predictor** .

## 4. Evaluation
Pada tahap ini, kita akan menggunakan data testing untuk mengevaluasi hasil dari training yang telah dilakukan sebelumnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas6/Mass%20Learning%20Event%20Prediction/dokum/evaluation.JPG?raw=true)

Data testing akan diproses dengan hasil dari node **PMML Compiler** sebelumnya menggunakan node **Compiled Model Predictor**.

Setelah itu, bisa dicek hasil scoring-nya menggunakan node **Entropy Scorer**. Berikut ini hasilnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas6/Mass%20Learning%20Event%20Prediction/dokum/entropy%20scorer%20quality.JPG?raw=true)

Ternyata kualitas prediksinya sebesar 77%.

## 5. Deployment
Pada tahap ini, hasil dari proses modelling akan digunakan untuk memprediksi data iris yang baru. (Fast Event Prediction).

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas6/Mass%20Learning%20Event%20Prediction/dokum/deployment.JPG?raw=true)

Untuk membuat data iris yang baru, kita memerlukan node **Container Input (JSON)**. Pada node tersebut, kita bisa memasukkan atribut-atribut iris sesuai yang kita inginkan. Berikut adalah konfigurasinya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas6/Mass%20Learning%20Event%20Prediction/dokum/json%20input%20atribut%20iris.JPG?raw=true)

Lalu, setelah proses pengisian tersebut selesai, format data yang semula adalah JSON akan  diubah ke dalam bentuk tabel menggunakan node **JSON to Table**. Berikut adalah konfigurasinya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas6/Mass%20Learning%20Event%20Prediction/dokum/json%20to%20table%20config.JPG?raw=true)

Setelah itu, data akan diproses dengan hasil dari node **PMML Compiler** sebelumnya menggunakan node **Compiled Model Predictor**.

Setelah itu, data akan dikonversi lagi ke dalam bentuk JSON menggunakan node **Table to JSON** dan data akan ditampilkan ke node **Container Output (JSON)**.

Berikut adalah hasil outputnya. 

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas6/Mass%20Learning%20Event%20Prediction/dokum/output%20cluster%20prediction%20json.JPG?raw=true)

Ternyata, data iris yang baru akan masuk ke cluster_0.

Terima Kasih.
