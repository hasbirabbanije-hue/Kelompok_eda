# Proyek Exploratory Data Analysis (EDA) — Tim Edan

Proyek ini merupakan bagian dari mata kuliah **Exploratory Data Analysis (KMTI21133)** STT Terpadu Nurul Fikri. Fokus proyek ini adalah melakukan analisis eksploratif terhadap dataset penjualan/karakter untuk menemukan pola, menguji validitas data, dan memberikan *insight* yang relevan.

---

## 📌 Milestone 1 (M1): Proposal Proyek & Akuisisi Data

Pada tahap **M1**, fokus utama tim adalah merumuskan arah penelitian, mengamankan dataset mentah, menyusun kamus data, dan memastikan privasi data terjamin.

### 1. Pertanyaan Analisis
* **PA-1:** Hari apa yang memiliki pinjaman sepeda terbanyak dalam satu hari?
* **PA-2:** Berapa rata-rata penggunaan sepeda permusim?
* **PA-3:** Apakah kecepata angin memengaruhi peminjaman sepeda perhari?

### 2. Dataset Mentah & Privasi Data
* **Sumber Data:** Dataset mentah disimpan di folder `data/raw/` dan **tidak akan pernah diubah secara manual** untuk menjaga keterlacakan (*traceability*).
* **Penanganan Data Pribadi (PII):** Seluruh kolom yang memuat data sensitif/pribadi telah divalidasi, di-hash, atau dianonimkan sebelum dipublikasikan ke repositori.
* **Kamus Data:** Dokumentasi variabel lengkap (nama kolom, tipe data, deskripsi, dan satuan) dapat dilihat pada berkas [`data_dictionary.md`](./data_dictionary.md).

---

## 🧹 Milestone 2 (M2): Diagnosis & Pembersihan Data (Data Quality Report)

Pada tahap **M2**, tim melakukan audit kualitas data secara komprehensif (Praktikum 4) dan mengeksekusi *pipeline* pembersihan berbasis keputusan analitis (Praktikum 5).

### 1. Ringkasan Diagnosis Kualitas Data (Praktikum 4)
Berdasarkan pengujian pada 5 Dimensi Kualitas Data:
* **Kelengkapan (*Completeness*):** Evaluasi pola *missing values* (MCAR/MAR/MNAR) dilakukan untuk memastikan kekosongan data ditangani tanpa merusak sebaran.
* **Keunikan (*Uniqueness*):** Pengujian duplikat persis maupun duplikat berbasis kunci utama (*primary key*).
* **Konsistensi (*Consistency*):** Pembersihan spasi ekstra (*trimming*) dan penyeragaman variasi teks/kategori.
* **Validitas (*Validity*):** Penyesuaian tipe data (misal konversi string tanggal ke format `datetime`) dan evaluasi rentang nilai numerik.
* **Akurasi (*Accuracy*):** Pengujian aturan bisnis dan korelasi logis antar-kolom.

### 2. Keputusan Pembersihan & Imputasi (Praktikum 5)
Setiap pembersihan dieksekusi secara otomatis melalui skrip/notebook dan **tidak menimpa data mentah**. Hasil pembersihan disimpan di `data/processed/`.

* **Perbaikan Tipe Data:** Kolom tanggal dikonversi menjadi `datetime` untuk analisis tren waktu.
* **Penyeragaman Teks:** Menggunakan peta keputusan eksplisit (`PETA_KATEGORI`) untuk menangani salah ketik dan sinonim.
* **Penanganan Anomali / Pelanggaran Bisnis:** Nilai yang melanggar logika bisnis (misal total hitungan tidak cocok) **ditandai dengan kolom penanda boolean** (bukan asal ditimpa/dihapus) agar tidak membiaskan analisis.
* **Cleaning Log:** Seluruh langkah, jumlah baris terdampak, dan alasan keputusan dicatat secara rinci di berkas [`cleaning_log.md`](./cleaning_log.md).

---

## 🛠️ Struktur Repositori

```text
.
├── README.md                 # Deskripsi proyek, M1, dan M2
├── data_dictionary.md        # Kamus data lengkap (M1)
├── cleaning_log.md           # Log keputusan pembersihan data (M2)
├── AI_usage_log.md           # Catatan penggunaan AI generatif
├── requirements.txt          # Dependensi pustaka Python
├── data/
│   ├── raw/                  # Data mentah asli (TIDAK BOLEH DIUBAH)
│   └── processed/            # Data hasil pembersihan (M2)
├── notebooks/
│   ├── 01_quality.ipynb      # Notebook diagnosis kualitas data (M2 - P4)
│   ├── 02_cleaning.ipynb     # Notebook pipeline pembersihan data (M2 - P5)
│   ├── 03_univariate.ipynb   # Notebook analisis univariat (M3)
│   └── 04_multivariate.ipynb # Notebook analisis bivariat/multivariat (M3)
└── reports/
    ├── data_quality_report.docx # Laporan Data Quality Report (M2)
    └── figures/              # Grafik & visualisasi laporan
