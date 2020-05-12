# Mass Prediction menggunakan Spark

Berikut adalah skema KNIME workflow-nya.

![workflow](https://github.com/Armunz/big-data/blob/master/tugas6/PMML%20to%20Spark%20Comprehensive%20Mode%20Learning%20Mass%20Prediction/dokum/workflow.JPG?raw=true)

## 1. Business Understanding
Pada kasus ini, kita bisa memprediksi class dari data iris secara batch (massal) menggunakan Spark.

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

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas6/PMML%20to%20Spark%20Comprehensive%20Mode%20Learning%20Mass%20Prediction/dokum/data%20preparation.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas6/PMML%20to%20Spark%20Comprehensive%20Mode%20Learning%20Mass%20Prediction/dokum/dataset%20iris.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas6/PMML%20to%20Spark%20Comprehensive%20Mode%20Learning%20Mass%20Prediction/dokum/data%20preparation%282%29.JPG?raw=true)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas6/PMML%20to%20Spark%20Comprehensive%20Mode%20Learning%20Mass%20Prediction/dokum/data%20tes%20iris.JPG?raw=true)

## 4. Modelling
Pada tahap ini, kita akan memodelkan data training dari iris tersebut ke dalam bentuk **PMML** (Predictive Model Markup Language). PMML adalah bentuk standard untuk merepresentasikan sebuah **predictive solutions**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas6/PMML%20to%20Spark%20Comprehensive%20Mode%20Learning%20Mass%20Prediction/dokum/modelling.JPG?raw=true)

Pada proses modelling ini tidak menggunakan Spark. Di sini digunakan dua algoritma yaitu **Decision Tree Learner** dan **RProp MLP Learner**.

Decision Tree adalah algoritma pengambilan keputusan menggunakan struktur data Tree (pohon). Berikut konfigurasi dan hasilnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas6/PMML%20to%20Spark%20Comprehensive%20Mode%20Learning%20Mass%20Prediction/dokum/decision%20tree%20config.JPG?raw=true)

Pada konfigurasi, kolom yang digunakan sebagai output adalah **class**, metode perhitungan menggunakan **Gini Index**,  dan tidak menggunakan **Pruning Method**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas6/PMML%20to%20Spark%20Comprehensive%20Mode%20Learning%20Mass%20Prediction/dokum/hasil%20decision%20tree.JPG?raw=true)

RProp MLP adalah algoritma berbasis NN (Neural Network) yang juga digunakan sebagai pengambilan keputusan. Berikut adalah konfigurasi dan hasil error plotnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas6/PMML%20to%20Spark%20Comprehensive%20Mode%20Learning%20Mass%20Prediction/dokum/Rprop%20config.JPG?raw=true)

Pada konfigurasi, jumlah iterasi maksimal adalah 100, jumlah hidden layers sebanyak 1, dan jumlah dari neuron per layer adalah 10. Kolom yang digunakan sebagai output adalah **class**.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas6/PMML%20to%20Spark%20Comprehensive%20Mode%20Learning%20Mass%20Prediction/dokum/error%20plot%20rprop.JPG?raw=true)

Algoritma RProp MLP di sini digunakan sebagai optimalisasi dari algoritma Decision Tree. 

Setelah proses dari kedua algoritma tersebut selesai, maka hasilnya semula berupa PMML akan diubah ke Cell, karena nantinya akan digabung. Proses konversi tersebut menggunakan node **PMML to Cell**.

Setelah itu, Hasilnya akan digabung (di-concatenate) menggunakan node **Concatenate**.

Lalu proses selanjutnya adalah mengkonversi kembali bentuk data yang semula Table menjadi PMML menggunakan node **Table to PMML Ensemble**. Agar data dalam bentuk PMML tadi dapat di compile, maka akan dikonversi ke  **Java**  menggunakan node  **PMML Compiler**.

Hasil dari node  **PMML Compiler**  nantinya akan digunakan sebagai  **Predictor** .

## 4. Evaluation
Pada tahap ini, kita akan menggunakan data testing untuk mengevaluasi hasil dari training yang telah dilakukan sebelumnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas6/PMML%20to%20Spark%20Comprehensive%20Mode%20Learning%20Mass%20Prediction/dokum/eval.JPG?raw=true)

Data testing akan diproses menggunakan Spark. Oleh karena itu dibutuhkan node **Create Local Big Data Environment** untuk membuat instance Big Data secara Local. 

Data testing diubah formatnya menjadi Spark menggunakan node **Table to Spark**.

Lalu, data Spark akan diproses dengan hasil dari node **PMML Compiler** sebelumnya menggunakan node **Spark Compiled Model Predictor**.

Lalu, data akan dikembalikan lagi formatnya menjadi table menggunakan node **Spark to Table** agar dapat dilakukan scoring (evaluasi).

Setelah itu, data akan dievaluasi menggunakan node **Scorer**. Berikut ini hasilnya.

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas6/PMML%20to%20Spark%20Comprehensive%20Mode%20Learning%20Mass%20Prediction/dokum/scorer.JPG?raw=true)

Ternyata, hasil accuracy-nya adalah sebesar 94%.

## 5. Deployment
Pada tahap ini, saya mendeploy hasil prediksi ke file csv menggunakan node **CSV Writer**. Berikut adalah hasilnya. [deploy.csv](https://github.com/Armunz/big-data/blob/master/tugas6/PMML%20to%20Spark%20Comprehensive%20Mode%20Learning%20Mass%20Prediction/deploy.csv)

![enter image description here](https://github.com/Armunz/big-data/blob/master/tugas6/PMML%20to%20Spark%20Comprehensive%20Mode%20Learning%20Mass%20Prediction/dokum/deploy.JPG?raw=true)

Terima Kasih.
