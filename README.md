# 📖 Kamus Data (Data Dictionary) - DiaBites

Dokumen ini berisi penjelasan detail mengenai struktur dataset `dataset_diabites_type_3labels.csv`. Dataset ini digunakan untuk melatih model Machine Learning (Klasifikasi) yang berfungsi memberikan rekomendasi keamanan konsumsi makanan/minuman kemasan, khususnya bagi penderita diabetes.

## 📊 Ringkasan Dataset
- **Nama File:** `dataset_diabites_type_3labels.csv`
- **Jumlah Baris:** 11.557 baris (hasil *Synthetic Augmentation*)
- **Jumlah Kolom:** 10 kolom (9 Fitur + 1 Target)
- **Tujuan Utama:** Memprediksi kelayakan produk untuk dikonsumsi berdasarkan nilai gizi per sajian yang disesuaikan dengan profil medis pengguna.

---

## 🗂️ Struktur Kolom (Fitur & Target)

Tabel berikut menjelaskan setiap variabel yang ada di dalam dataset:

| Nama Kolom | Peran | Tipe Data | Satuan | Deskripsi | Keterangan / Nilai Valid |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `product_name` | Identifier | String | - | Nama produk makanan atau minuman kemasan. (Hanya untuk referensi, dibuang saat *training* model). | Bebas (Teks) |
| `sugar_g` | Feature | Float | Gram (g) | Kandungan gula (Sugar) per takaran saji. | `0.0` hingga `>100.0` |
| `carbs_g` | Feature | Float | Gram (g) | Kandungan karbohidrat total (Carbs) per takaran saji. | `0.0` hingga `>200.0` |
| `calories` | Feature | Float | Kkal | Kandungan energi total (Calories) per takaran saji. | `0.0` hingga `>1000.0` |
| `sodium_mg` | Feature | Float | Miligram (mg) | Kandungan natrium/sodium per takaran saji. | `0.0` hingga `>5000.0` |
| `fat_g` | Feature | Float | Gram (g) | Kandungan lemak total (Fat) per takaran saji. | `0.0` hingga `>100.0` |
| `age_group` | Feature | Integer | - | Kategori rentang usia pengguna (hasil *encoding*). | `0`: Anak-anak (Child)<br>`1`: Dewasa (Adult)<br>`2`: Lansia (Elderly) |
| `bmi_category` | Feature | Integer | - | Kategori Indeks Massa Tubuh pengguna (hasil *encoding*). | `0`: Underweight<br>`1`: Normal<br>`2`: Overweight<br>`3`: Obese |
| `diabetes_type` | Feature | Integer | - | Klasifikasi medis status diabetes pengguna. | `0`: Tidak Diabetes<br>`1`: Tipe 1 (Insulin Dependent)<br>`2`: Tipe 2 (Insulin Resistance) |
| **`label`** | **Target** | String | - | Output klasifikasi rekomendasi dari sistem pakar/aturan medis. | `Recommended`, `Caution`, `Not Recommended` |

---

## 🧬 Data Lineage (Asal-usul Data) & Preprocessing

Bagi *Data Scientist* atau *Reviewer* yang menggunakan dataset ini, harap perhatikan hal-hal berikut:

### 1. Proses Augmentasi Sintetis (Data Augmentation)
Dataset ini dibangun dari ±1.600 data produk gizi unik yang beredar di pasaran. Untuk melatih kemampuan **Personalisasi AI**, kami menerapkan teknik *Cross-Join Augmentation*. 
Satu data produk diekspansi menjadi 7 baris yang berbeda untuk merepresentasikan profil pengguna yang berbeda (contoh: diuji oleh profil orang normal, profil diabetes tipe 1, tipe 2, lansia, dll). Hal ini membuat total data menjadi 11.557 baris.

### 2. Standar Aturan Medis (Pelabelan)
Kolom `label` dibuat menggunakan algoritma penilaian bersyarat (Rule-based Scoring) yang sangat memprioritaskan keamanan penderita diabetes. 
- **Diabetes Tipe 1:** Penalti berat (*heavy penalty*) diberikan pada produk dengan Karbohidrat > 30g dan Gula > 10g.
- **Diabetes Tipe 2:** Penalti berat diberikan pada produk dengan Kalori > 200 Kkal, Lemak > 10g, dan Gula > 10g.
- **Imbalance Data:** Karena aturan yang sangat ketat, mayoritas data bagi penderita diabetes akan condong ke label `Not Recommended` atau `Caution`. Disarankan menggunakan teknik **SMOTE** saat melatih model.

### 3. Persiapan untuk Machine Learning (Ready-to-Process)
- Data *missing value* (NaN) pada nilai gizi telah diatasi dengan imputasi nilai `0` (kecuali jika seluruh nilai gizi adalah `0` yang dianggap data cacat dan telah dihapus).
- Data teks pada fitur profil (`age`, `bmi`, `diabetes`) **sudah di-*encode*** menjadi bentuk integer, sehingga dapat langsung dibaca oleh algoritma *Machine Learning*.
- **Wajib:** Terapkan `StandardScaler` (atau *scaler* sejenis) pada 5 kolom fitur nutrisi numerik pertama (`sugar`, `carbs`, `calories`, `sodium`, `fat`) sebelum masuk ke fase *training* model, karena perbedaan skala antara Gram dan Miligram.

---
*Dikelola oleh Tim Data Science - DiaBites*
