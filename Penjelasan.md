# Laporan Proyek Analisis Big Data: Prediksi Harga Berlian dengan PySpark

Laporan ini disusun untuk memenuhi kriteria ujian mata kuliah Big Data Analytics, mencakup pipeline data dari pengolahan file system hingga evaluasi model machine learning.

## 1. Studi Kasus & Dataset (Big Data 5V)
Proyek ini menggunakan dataset **Diamonds** yang merepresentasikan karakteristik Big Data:
* **Volume**: Dataset memiliki puluhan ribu baris data yang memerlukan pemrosesan paralel terdistribusi.
* **Variety**: Mencakup data terstruktur numerik (*carat, x, y, z*) dan data kategorikal (*cut, color, clarity*).
* **Veracity**: Diperlukan tahapan pembersihan data dan pengecekan tipe data untuk memastikan keakuratan hasil prediksi.

## 2. Infrastruktur & Pipeline Data
* **File System**: Dataset disimpan dan dikelola menggunakan **HDFS** (Hadoop Distributed File System) untuk mendukung skalabilitas penyimpanan.
* **Framework**: Pemrosesan dilakukan menggunakan **PySpark** untuk menangani eksekusi data secara *in-memory*.

### Langkah Pemrosesan (Nomor 3 a-e):
1.  **Batch Processing (MapReduce)**: Menggunakan transformasi RDD untuk menghitung distribusi data (misal: jumlah berlian per kategori *cut*).
2.  **EDA (Exploratory Data Analysis)**: Menggunakan Spark SQL dan visualisasi untuk memahami korelasi antara *carat* dan *price*.
3.  **Preprocessing**: Melakukan casting tipe data ke `Double` untuk koordinat (x, y, z) dan penanganan *missing values* untuk menjamin kualitas input.
4.  **Manipulasi Data**: Penggunaan Spark SQL dengan teknik **CTE (Common Table Expressions)** untuk menyaring data harga di atas rata-rata.
5.  **Operasi Partisi**: Implementasi `reduceByKey` dan `partitionBy` pada RDD untuk optimalisasi distribusi beban kerja pada cluster.

## 3. Feature Engineering & Algoritma (Nomor 4)
Kami melakukan modifikasi fitur untuk meningkatkan ketajaman model:
* **Volume Feature**: Membuat fitur baru `volume` ($x \times y \times z$) karena dimensi fisik berkorelasi kuat dengan harga selain berat (*carat*).
* **Ordinal Encoding**: Menggunakan `StringIndexer` untuk fitur kategorikal. Pilihan ini diambil karena *cut, color,* dan *clarity* memiliki tingkatan kualitas (hierarki) yang bisa dipelajari secara efektif oleh algoritma berbasis pohon.

### Strategi Seleksi Model:
**Opsi**: Menggunakan **20% sampel data** untuk seleksi awal.
**Alasan**: Untuk efisiensi waktu komputasi. GBT adalah algoritma yang berat, sehingga seleksi algoritma terbaik dilakukan pada sampel sebelum melakukan *all-in* pada data penuh.

**Komparasi Baseline (Sampel 20%):**
* **Random Forest**: RMSE 1090.28 | R2 0.9249
* **GBT (Gradient Boosted Trees)**: RMSE 825.35 | R2 0.9570 (Pemenang)

## 4. Hyperparameter Tuning (Nomor 5)
Setelah GBT terpilih, kami melakukan tuning menggunakan **100% data training** dengan `TrainValidationSplit` untuk mencari parameter optimal.

**Parameter yang digunakan dalam Grid Search:**
* `maxDepth`: [5, 10] (Mencari kedalaman pohon optimal untuk menangkap pola non-linear).
* `maxIter`: [20, 50] (Menambah jumlah iterasi boosting untuk memperkecil residual error).

**Analisis Kenaikan Performa (Baseline vs Tuned):**
| Metrik | GBT Baseline (Sampel) | GBT Final (Tuned & Full Data) | Peningkatan |
| :--- | :--- | :--- | :--- |
| **RMSE** | 825.3568 | **629.6752** | **+23.7%** (Error turun) |
| **MAE** | 444.4212 | **345.5663** | **+22.2%** (Akurasi naik) |
| **R2** | 0.9570 | **0.9756** | **+1.9%** (Mendekati Sempurna) |

## 5. Evaluasi Akhir (Nomor 6)
Evaluasi akhir menggunakan 4 metrik regresi utama untuk memastikan model tidak *overfit* dan memiliki generalisasi yang baik:

1.  **RMSE (629.6752)**: Deviasi harga prediksi terhadap harga asli dalam USD.
2.  **MSE (396490.9203)**: Rata-rata kuadrat kesalahan untuk memberi penalti pada outlier.
3.  **MAE (345.5663)**: Rata-rata absolut kesalahan harga (melesat sekitar $345 dari harga asli).
4.  **R2 (0.9756)**: Model mampu menjelaskan **97.56%** variansi harga berlian.

## 6. Interpretasi Visual
Visualisasi 100 data menunjukkan bahwa model GBT sangat *robust*. Garis prediksi berhasil mengikuti fluktuasi harga asli dengan sangat rapat. Model ini sangat efektif untuk digunakan dalam aplikasi penentuan harga (pricing engine) otomatis di industri perhiasan.

# Laporan Proyek Analisis Big Data: Prediksi Harga Berlian dengan PySpark

Laporan ini disusun untuk memenuhi kriteria ujian mata kuliah Big Data Analytics, mencakup pipeline data dari pengolahan file system hingga evaluasi model machine learning.

## 1. Studi Kasus & Dataset (Big Data 5V)
Proyek ini menggunakan dataset **Diamonds** yang merepresentasikan karakteristik Big Data:
* **Volume**: Dataset memiliki puluhan ribu baris data yang memerlukan pemrosesan paralel terdistribusi.
* **Variety**: Mencakup data terstruktur numerik (*carat, x, y, z*) dan data kategorikal (*cut, color, clarity*).
* **Veracity**: Diperlukan tahapan pembersihan data dan pengecekan tipe data untuk memastikan keakuratan hasil prediksi.

## 2. Infrastruktur & Pipeline Data
* **File System**: Dataset disimpan dan dikelola menggunakan **HDFS** (Hadoop Distributed File System) untuk mendukung skalabilitas penyimpanan.
* **Framework**: Pemrosesan dilakukan menggunakan **PySpark** untuk menangani eksekusi data secara *in-memory*.

### Langkah Pemrosesan (Nomor 3 a-e):
1.  **Batch Processing (MapReduce)**: Menggunakan transformasi RDD untuk menghitung distribusi data (misal: jumlah berlian per kategori *cut*).
2.  **EDA (Exploratory Data Analysis)**: Menggunakan Spark SQL dan visualisasi untuk memahami korelasi antara *carat* dan *price*.
3.  **Preprocessing**: Melakukan casting tipe data ke `Double` untuk koordinat (x, y, z) dan penanganan *missing values* untuk menjamin kualitas input.
4.  **Manipulasi Data**: Penggunaan Spark SQL dengan teknik **CTE (Common Table Expressions)** untuk menyaring data harga di atas rata-rata.
5.  **Operasi Partisi**: Implementasi `reduceByKey` dan `partitionBy` pada RDD untuk optimalisasi distribusi beban kerja pada cluster.

## 3. Feature Engineering & Algoritma (Nomor 4)
Kami melakukan modifikasi fitur untuk meningkatkan ketajaman model:
* **Volume Feature**: Membuat fitur baru `volume` ($x \times y \times z$) karena dimensi fisik berkorelasi kuat dengan harga selain berat (*carat*).
* **Ordinal Encoding**: Menggunakan `StringIndexer` untuk fitur kategorikal. Pilihan ini diambil karena *cut, color,* dan *clarity* memiliki tingkatan kualitas (hierarki) yang bisa dipelajari secara efektif oleh algoritma berbasis pohon.

### Strategi Seleksi Model:
**Opsi**: Menggunakan **20% sampel data** untuk seleksi awal.
**Alasan**: Untuk efisiensi waktu komputasi. GBT adalah algoritma yang berat, sehingga seleksi algoritma terbaik dilakukan pada sampel sebelum melakukan *all-in* pada data penuh.

**Komparasi Baseline (Sampel 20%):**
* **Random Forest**: RMSE 1090.28 | R2 0.9249
* **GBT (Gradient Boosted Trees)**: RMSE 825.35 | R2 0.9570 (Pemenang)

## 4. Hyperparameter Tuning (Nomor 5)
Setelah GBT terpilih, kami melakukan tuning menggunakan **100% data training** dengan `TrainValidationSplit` untuk mencari parameter optimal.

**Parameter yang digunakan dalam Grid Search:**
* `maxDepth`: [5, 10] (Mencari kedalaman pohon optimal untuk menangkap pola non-linear).
* `maxIter`: [20, 50] (Menambah jumlah iterasi boosting untuk memperkecil residual error).

**Analisis Kenaikan Performa (Baseline vs Tuned):**
| Metrik | GBT Baseline (Sampel) | GBT Final (Tuned & Full Data) | Peningkatan |
| :--- | :--- | :--- | :--- |
| **RMSE** | 825.3568 | **629.6752** | **+23.7%** (Error turun) |
| **MAE** | 444.4212 | **345.5663** | **+22.2%** (Akurasi naik) |
| **R2** | 0.9570 | **0.9756** | **+1.9%** (Mendekati Sempurna) |

## 5. Evaluasi Akhir (Nomor 6)
Evaluasi akhir menggunakan 4 metrik regresi utama untuk memastikan model tidak *overfit* dan memiliki generalisasi yang baik:

1.  **RMSE (629.6752)**: Deviasi harga prediksi terhadap harga asli dalam USD.
2.  **MSE (396490.9203)**: Rata-rata kuadrat kesalahan untuk memberi penalti pada outlier.
3.  **MAE (345.5663)**: Rata-rata absolut kesalahan harga (melesat sekitar $345 dari harga asli).
4.  **R2 (0.9756)**: Model mampu menjelaskan **97.56%** variansi harga berlian.

## 6. Interpretasi Visual
Visualisasi 100 data menunjukkan bahwa model GBT sangat *robust*. Garis prediksi berhasil mengikuti fluktuasi harga asli dengan sangat rapat. Model ini sangat efektif untuk digunakan dalam aplikasi penentuan harga (pricing engine) otomatis di industri perhiasan.

![WhatsApp Image 2026-01-30 at 15 29 25](https://github.com/user-attachments/assets/975310b6-9d67-4c89-baed-b0c5cb2442f7)
![WhatsApp Image 2026-01-30 at 15 29 34](https://github.com/user-attachments/assets/5b930fcd-64c0-43db-bda1-947b843dafa4)

