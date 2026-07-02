# Network Traffic Analysis: Uncovering Brute Force Attack Patterns

## Project Overview
Keamanan jaringan sangat bergantung pada kemampuan mendeteksi anomali pada lalu lintas data (*network flow*). Proyek analitik data ini bertujuan untuk mengeksplorasi dataset lalu lintas jaringan guna menemukan karakteristik teknis dan pola unik yang membedakan aktivitas pengguna normal (Benign) dengan serangan **Brute Force (SSH/FTP)**.

* **Dataset:** [CSE-CIC-IDS2018](https://www.kaggle.com/datasets/dhoogla/csecicids2018) (Format `.parquet`)
* **Tools & Libraries:** Python, Pandas, NumPy, Matplotlib, Seaborn, Kagglehub.

---

## Methodology
Proyek ini dieksekusi melalui beberapa tahapan utama:
1. **Data Extraction:** Mengunduh dan memuat data berformat `.parquet` yang berukuran besar dan terkompresi menggunakan *library* `kagglehub` secara langsung di *cloud environment*.
2. **Data Cleaning:** Menstandarisasi penamaan kolom dan membersihkan anomali data numerik (mengubah nilai *Infinity* menjadi *NaN* lalu menghapus *missing values*).
3. **Exploratory Data Analysis (EDA):** Membandingkan distribusi volume dan menghitung rata-rata durasi aliran data antara *traffic* normal dan *malicious*.
4. **Feature Engineering & Correlation:** Melakukan *binary encoding* pada label target dan menggunakan *Pearson Correlation* (divisualisasikan dengan *Heatmap*) untuk menemukan fitur teknis yang memiliki korelasi tertinggi dengan serangan.

---

## Key Insights & Findings

### 1. The Automation Footprint (Anomali Durasi Koneksi)
Terdapat perbedaan yang sangat drastis pada `Flow Duration`:
- **Traffic Normal:** Memiliki durasi koneksi yang panjang dan stabil.
- **Traffic Serangan:** Memiliki durasi yang sangat singkat, nyaris menyentuh angka nol. 
> **Analisis:** Pola ini menunjukkan jejak penggunaan *script* otomatis. Penyerang melakukan ribuan percobaan *login* secara masif; ketika proses otentikasi gagal, koneksi langsung diputus secara otomatis, sehingga meninggalkan jejak durasi per paket data yang sangat minim.

### 2. Top Correlated Technical Indicators
Berdasarkan analisis korelasi matriks, ditemukan dua fitur teknis utama yang paling kuat mengindikasikan terjadinya serangan *brute force*:
- **`Fwd Act Data Packets` (Korelasi: 0.72):** Tingginya jumlah paket data aktif yang dikirimkan ke arah *server* (Forward) sangat mencerminkan intensitas percobaan *login* beruntun.
- **`Fwd Seg Size Min` (Korelasi: 0.71):** Ukuran minimum segmen *forward* memiliki korelasi yang tinggi, menunjukkan bahwa *payload* (isi data) yang dikirimkan penyerang memiliki struktur ukuran yang spesifik, konsisten, dan berulang-ulang saat *tools* otomatis menebak kredensial.

### 3. Attack Volume & Targeting
Pada rentang waktu data diobservasi, terdeteksi **94.048 aliran data** yang teridentifikasi sebagai serangan `SSH-Bruteforce`. Volume ini jauh lebih masif dibandingkan dengan percobaan pada *port* FTP yang hanya mencatat 53 kejadian. Hal ini mengindikasikan bahwa *port* SSH menjadi target utama penetrasi pada infrastruktur jaringan tersebut.

---

## How to Run
Untuk melihat kode secara lengkap dan menjalankan analisis ini secara mandiri:
1. Buka file Jupyter Notebook (`network-intrusion-eda.ipynb`) yang ada di *repository* ini.
2. Anda juga dapat menjalankannya langsung di cloud melalui tombol **Open in Colab** (jika tersedia).
