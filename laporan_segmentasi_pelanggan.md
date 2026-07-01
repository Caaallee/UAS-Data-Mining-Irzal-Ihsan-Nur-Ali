# Laporan Analisis Segmentasi Pelanggan dengan K-Means Clustering

## 1. Ringkasan Eksekutif

Laporan ini menyajikan hasil analisis segmentasi pelanggan menggunakan metode **K-Means Clustering** berdasarkan tiga variabel utama: usia, pendapatan tahunan, dan skor pengeluaran. Dari 235 baris data mentah, setelah proses pembersihan diperoleh **220 pelanggan bersih** yang berhasil dikelompokkan ke dalam **4 segmen (cluster)** dengan karakteristik yang berbeda-beda. Hasil segmentasi ini dapat digunakan sebagai dasar strategi pemasaran yang lebih tepat sasaran untuk setiap kelompok pelanggan.

## 2. Sumber Data

| File | Deskripsi | Dimensi |
|---|---|---|
| `dataset_pelanggan_mentah.csv` | Data pelanggan sebelum diproses (raw) | 235 baris x 5 kolom |
| `proyek_akhir_clustering.ipynb` | Notebook Python berisi seluruh proses analisis | 8 sel kode |
| `dataset_pelanggan_terkluster.csv` | Data hasil akhir setelah clustering | 220 baris x 7 kolom |

Kolom pada data mentah: `CustomerID`, `Usia`, `Pendapatan_Tahunan_juta`, `Skor_Pengeluaran_1_100`, `Kategori_Member` (Regular/Premium).

## 3. Metodologi

Proses analisis pada notebook mengikuti enam tahap berikut:

### Langkah 1 — Memuat Dataset
Dataset mentah dimuat dengan dimensi awal **235 baris x 5 kolom**.

### Langkah 2 — Cleaning Data & Preprocessing
- **Duplikasi**: ditemukan dan dihapus **15 baris data duplikat**.
- **Penanganan outlier/nilai tidak valid**: nilai dianggap tidak wajar diubah menjadi `NaN` dengan aturan:
  - `Usia > 90` → dianggap tidak valid
  - `Skor_Pengeluaran_1_100 < 1` → dianggap tidak valid
  - `Pendapatan_Tahunan_juta > 200` → dianggap tidak valid
- **Imputasi nilai kosong**: seluruh nilai `NaN` diisi menggunakan **nilai median** dari masing-masing kolom.
- **Encoding kategorikal**: `Kategori_Member` diubah menjadi numerik (`Regular = 0`, `Premium = 1`).
- Hasil akhir setelah pembersihan: **220 baris data bersih**.

### Langkah 3 — Feature Scaling
Tiga fitur numerik (`Usia`, `Pendapatan_Tahunan_juta`, `Skor_Pengeluaran_1_100`) distandarisasi menggunakan `StandardScaler` agar memiliki skala yang setara sebelum proses clustering.

### Langkah 4 — Penentuan Jumlah Cluster (Metode Elbow)
Algoritma K-Means dijalankan untuk k = 1 sampai 10, dan nilai WCSS (Within-Cluster Sum of Squares) diplot untuk menentukan titik "siku" (elbow). Berdasarkan grafik ini, jumlah cluster optimal yang dipilih adalah **k = 4**.

### Langkah 5 — Pelatihan Model K-Means Final
Model K-Means dilatih dengan `k=4`, menghasilkan:
- **Silhouette Score = 0.2301**

Skor ini berada pada kategori sedang-lemah, menandakan pemisahan antar cluster cukup terlihat namun ada sejumlah overlap antar segmen — hal yang wajar untuk data perilaku pelanggan yang bersifat kontinu.

### Langkah 6 — Profiling Cluster
Setiap pelanggan diberi label cluster (0–3), lalu dianalisis rata-rata karakteristiknya dan disimpan ke `dataset_pelanggan_terkluster.csv`.

## 4. Hasil Segmentasi

### 4.1 Ukuran dan Profil Rata-Rata Tiap Cluster

| Cluster | Jumlah Pelanggan | Rata-rata Usia | Rata-rata Pendapatan (juta) | Rata-rata Skor Pengeluaran |
|---|---|---|---|---|
| 0 | 56 | 43.9 | 83.5 | 34.0 |
| 1 | 56 | 44.1 | 59.2 | 67.0 |
| 2 | 57 | 37.0 | 48.6 | 33.5 |
| 3 | 51 | 24.7 | 70.4 | 59.5 |

### 4.2 Komposisi Kategori Member per Cluster

| Cluster | Premium | Regular | % Premium |
|---|---|---|---|
| 0 | 24 | 32 | 42.9% |
| 1 | 27 | 29 | 48.2% |
| 2 | 27 | 30 | 47.4% |
| 3 | 17 | 34 | 33.3% |

### 4.3 Interpretasi Segmen

**Cluster 0 — "Berpendapatan Tinggi, Hemat" (56 pelanggan)**
Usia paruh baya (~44 tahun) dengan pendapatan tertinggi di antara semua cluster (~83 juta), namun skor pengeluaran rendah (~34). Kelompok ini kemungkinan adalah pelanggan yang mampu secara finansial tetapi selektif atau jarang berbelanja — berpotensi menjadi target kampanye yang menonjolkan nilai/eksklusivitas produk.

**Cluster 1 — "Pengeluaran Tinggi, Loyal" (56 pelanggan)**
Usia paruh baya (~44 tahun) dengan pendapatan menengah (~59 juta) tetapi skor pengeluaran tertinggi (~67). Ini adalah segmen paling bernilai dari sisi perilaku belanja — kandidat utama untuk program loyalitas dan penawaran retensi.

**Cluster 2 — "Pendapatan Rendah, Hemat" (57 pelanggan)**
Usia relatif muda (~37 tahun), pendapatan terendah (~49 juta), dan skor pengeluaran rendah (~33). Segmen ini paling sensitif terhadap harga; cocok ditargetkan dengan promo, diskon, atau paket hemat.

**Cluster 3 — "Muda, Pengeluaran Tinggi" (51 pelanggan)**
Kelompok termuda (~25 tahun) dengan pendapatan menengah-tinggi (~70 juta) dan skor pengeluaran cukup tinggi (~59). Proporsi Premium terendah (33.3%), mengindikasikan potensi besar untuk dikonversi dari Regular menjadi Premium melalui penawaran yang relevan dengan gaya hidup muda.

## 5. Visualisasi

Notebook menghasilkan dua visualisasi utama:
1. **Grafik Metode Elbow** — menunjukkan penurunan WCSS terhadap jumlah cluster (k=1 s.d. 10), digunakan untuk menentukan k optimal = 4.
2. **Scatter Plot Hasil Cluster** — memplot `Pendapatan_Tahunan_juta` vs `Skor_Pengeluaran_1_100`, dengan setiap cluster diberi warna berbeda, memperlihatkan pemisahan visual antar segmen pelanggan.

*(Catatan: kedua grafik dihasilkan langsung di dalam notebook melalui `plt.show()` dan tidak disimpan sebagai file gambar terpisah, sehingga tidak disertakan sebagai gambar statis dalam laporan ini.)*

## 6. Kesimpulan dan Rekomendasi

1. **Kualitas data**: Proses cleaning berhasil menangani duplikasi dan nilai tidak wajar (usia > 90, pendapatan > 200 juta, skor pengeluaran < 1) dengan imputasi median, menghasilkan dataset yang cukup bersih (220 dari 235 baris awal, atau ~93.6% data terpakai).
2. **Kualitas cluster**: Silhouette Score 0.2301 menunjukkan pengelompokan yang moderat — cukup baik untuk insight bisnis awal, namun dapat ditingkatkan lebih lanjut (lihat rekomendasi di bawah).
3. **Segmen paling bernilai**: Cluster 1 (pengeluaran tinggi, loyal) adalah target retensi utama, sementara Cluster 3 (muda, berpotensi tinggi) adalah target akuisisi/konversi Premium.
4. **Segmen sensitif harga**: Cluster 0 dan Cluster 2 memiliki skor pengeluaran rendah meski profil pendapatan berbeda — strategi pendekatan untuk keduanya perlu dibedakan (Cluster 0 lebih ke arah nilai/eksklusivitas, Cluster 2 lebih ke arah harga/promo).

**Saran pengembangan lanjutan:**
- Menguji algoritma clustering lain (misalnya DBSCAN atau Gaussian Mixture Model) untuk membandingkan kualitas segmentasi.
- Menambahkan fitur perilaku tambahan (frekuensi transaksi, riwayat kategori produk, dsb.) jika tersedia, untuk memperkaya profil setiap segmen.
- Melakukan validasi bisnis terhadap masing-masing segmen bersama tim pemasaran sebelum eksekusi kampanye.