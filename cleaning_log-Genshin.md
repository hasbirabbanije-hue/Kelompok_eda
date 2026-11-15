# Cleaning Log

**Tim:** Yusuf
**Dataset:** genshin_impact.csv
**Baris mentah:** 103 → **baris bersih:** 103
**Terakhir diperbarui:** 9 November 2026

## Urutan langkah

| # | Langkah | Kolom | Baris terdampak | Keputusan | Alasan |
|---|---------|-------|-----------------|-----------|--------|
| 1 | Perbaiki tipe data | `release_date` | 103 baris | Konversi ke datetime dengan `pd.to_datetime` | Tanggal masih berupa teks sehingga tidak bisa diurutkan atau dihitung selisihnya. Hasil divalidasi: `release_date` + `days_since_release` menghasilkan tepat satu tanggal (4 Januari 2026) pada seluruh 103 baris, dan rentangnya 28/09/2020 sampai 03/12/2025. Format sumber seragam (yyyy-mm-dd), jadi tidak ada risiko hari dan bulan tertukar. |
| 2 | Perbaiki tipe data | `rarity` | 103 baris | Jadikan kategori berurutan (4 < 5) | Hanya bernilai 4 dan 5. Ini tingkatan, bukan besaran, sehingga rata-rata 4,56 tidak punya arti. Dibiarkan numerik ditolak karena berisiko dirata-ratakan. |
| 3 | Rapikan struktur kolom | `Unnamed: 0` | 103 baris | Buang kolom | Hanya sisa indeks 0 sampai 102 yang sama dengan nomor baris. Dipertahankan ditolak karena berisiko menghasilkan korelasi semu dengan urutan baris. |
| 4 | Rapikan struktur kolom | `3D-model` | 103 baris | Ganti nama menjadi `model_3d` | Nama diawali angka dan bertanda hubung sehingga tidak bisa dipakai sebagai atribut atau di formula. Hanya nama yang berubah, isinya tidak. |
| 5 | Buang spasi pada kolom teks | 10 kolom teks (`d.select_dtypes`) | 0 baris | Diperiksa dengan `str.strip()` pada seluruh kolom teks, tidak ada yang perlu diubah | Memastikan tidak ada spasi tersembunyi yang memecah `groupby` atau melambungkan `nunique`. Hasilnya bersih, dicatat sebagai bukti bahwa langkah ini sudah diperiksa. |
| 6 | Placeholder menjadi kosong | Seluruh kolom teks dan numerik | 0 baris | Dipindai placeholder teks (N/A, null, -, unknown, dan sejenisnya) dan nilai numerik ekstrem (-999, 0). Tidak ada yang ditemukan. | Nilai minimum kolom numerik selalu positif (selain penanda 0/1 pada `is_standard_banner` dan `is_archon`). Tidak ada nilai yang menyamar sebagai data, sehingga tidak ada yang diubah menjadi NaN. |
| 7 | Seragamkan kategori | `region` | 1 baris | Perbaiki `Extra-Terresterial` menjadi `Extra-Terrestrial` lewat dictionary | Salah ketik yang jelas pada satu karakter (skirk). `Nod-Krai` (6 karakter) dibiarkan karena wilayah yang sah, bukan salah ketik. Jumlah kategori tetap 9 karena hanya ejaan yang berubah. |
| 8 | Tangani duplikat | Seluruh kolom dan `name` | 0 baris | Tidak ada yang dihapus | `duplicated()` menghasilkan 0 dan `name` unik pada 103 baris, jadi tidak ada duplikat persis maupun duplikat kunci. Setiap karakter muncul satu kali. |
| 9 | Tangani nilai mustahil | `rarity`, `lvl_90_HP/ATK/DEF`, `duplicate_rate`, `c6_rate`, `days_since_release` | 0 baris | Tidak ada nilai yang diubah | Seluruh aturan rentang lolos: `rarity` hanya 4 atau 5, HP/ATK/DEF positif, kedua rate di antara 0 dan 1, dan tidak ada hari negatif. |
| 10 | Tangani hubungan antarkolom | `pulled_count` | 9 baris | Ditandai dengan kolom `pulled_tidak_cocok`, tidak ditimpa | `pulled_count` tidak sama dengan jumlah `constellation_0` sampai `constellation_7` (selisih 1 sampai 3 pull, maksimum 0,0011% nilai baris). Belum diketahui kolom mana yang benar, jadi menimpa sama saja dengan menebak. Membuang 9 baris ditolak karena menggeser median `pulled_count` sebesar −19,5% (652.085 menjadi 524.942,5), jauh lebih besar daripada kesalahan yang ingin diperbaiki. |
| 11 | Tangani nilai kosong | Seluruh kolom | 0 baris | Tidak ada yang diisi atau dibuang | `isna().sum()` menghasilkan 0 pada seluruh kolom, sehingga klasifikasi MCAR, MAR, atau MNAR tidak diperlukan. |
| 12 | Buat penanda | `banner_versions` | 41 baris | Tambah kolom `ada_label_luna` | Dua format bercampur: nomor versi (mis. 5.5) dan label Luna I sampai Luna IV. Penanda memungkinkan analisis berbasis nomor versi dibatasi pada 62 baris tanpa harus menebak pemetaan. |
| 13 | Buat penanda | `banner_dates` | 7 baris | Tambah kolom `banner_belum_selesai` | Banner 7 karakter (varesa, iansan, gaming, chevreuse, xilonen, ineffa, neuvillette) berakhir setelah tanggal data (4 Januari 2026), sehingga `pulled_count` mereka belum tentu final. Membuang 7 baris ditolak karena menggeser mean `pulled_count` sebesar +4,1% dan menghilangkan 7% data yang masih valid pada kolom lain. |

## Peta keputusan manual

Penyeragaman yang tidak bisa diputuskan mesin: salah ketik, ejaan, dan penamaan kolom.

| Nilai asal | Diubah menjadi | Dasar keputusan |
|------------|----------------|-----------------|
| `Extra-Terresterial` | `Extra-Terrestrial` | Salah ketik: ejaan baku bahasa Inggris adalah *extraterrestrial*. Hanya 1 baris (skirk), jumlah kategori tidak berubah. |
| `3D-model` | `model_3d` | Nama diawali angka dan bertanda hubung tidak valid sebagai nama variabel Python. |

## Yang sengaja TIDAK diubah

| Masalah | Mengapa dibiarkan | Akibat bagi analisis |
|---------|-------------------|----------------------|
| Label `Luna I` sampai `Luna IV` pada `banner_versions` (41 baris) | Pemetaan ke nomor versi tidak dapat diverifikasi dari data, dan menebaknya menciptakan data palsu. | Ditandai `ada_label_luna`. Analisis berbasis nomor versi dibatasi pada 62 baris, sedangkan analisis jumlah banner (`num_banners`) tidak terpengaruh karena sudah sama dengan panjang daftar pada 103 baris. |
| `pulled_count` tidak sama dengan jumlah C0–C7 (9 baris) | Belum diketahui kolom mana yang salah. Selisihnya hanya sekitar 0,001% dari nilai baris. | Ditandai `pulled_tidak_cocok`. Seandainya ditimpa, mean berubah hanya +0,13 pull. Analisis utama diulang tanpa baris bertanda untuk menguji apakah kesimpulan berubah. |
| Banner yang berakhir setelah tanggal data (7 baris) | Jumlah pull kemungkinan lebih rendah dari nilai akhir, tetapi besarnya tidak diketahui. | Ditandai `banner_belum_selesai`. Analisis dijalankan dengan dan tanpa baris tersebut (n = 96 tanpa penanda). |
| Urutan `banner_dates` tidak kronologis (22 baris) dan daftar tersimpan sebagai teks (103 baris) | CSV tidak mendukung list, dan urutan asli sumber tidak merusak jumlah atau isinya. | Daftar di-parse dengan `ast.literal_eval` dan diurutkan hanya saat dipakai. |
| `duplicate_rate` dan `avg_copies_per_player` redundan (103 baris, korelasi Spearman 1,00) | Kolom turunan tidak merusak kebersihan data, dan keberadaannya memungkinkan verifikasi silang antarkolom. | Hanya satu dari pasangan ini yang dipakai pada korelasi dan model, agar tidak dihitung sebagai dua bukti terpisah. |
| `Nod-Krai` pada `region` (6 baris) | Wilayah yang sah di game, bukan salah ketik. Berdasarkan pengetahuan domain tim, belum diverifikasi ke sumber resmi. | Dipertahankan sebagai kategori tersendiri. |

## Pertanyaan yang belum terjawab

Hal yang perlu dikonfirmasi ke pemilik data atau dosen.

- [ ] Sumber asli, lisensi, dan tanggal pengunduhan dataset Genshin Impact (provenans belum terisi).
- [ ] Cara `pulled_count` dihitung (sumber, wilayah, dan rentang waktu), karena tanpa itu makna "popularitas" hanyalah asumsi.
- [ ] Apakah dataset ini (103 baris, hampir bersih) boleh dipakai, atau perlu tambahan "pengotor" sesuai panduan.
- [ ] Pemetaan resmi label `Luna I` sampai `Luna IV` ke nomor versi game.

## Dampak keseluruhan

| Ukuran | Sebelum | Sesudah | Catatan |
|--------|---------|---------|---------|
| Jumlah baris | 103 | 103 | Tidak ada baris yang dibuang. |
| Jumlah kolom | 32 | 34 | Dibuang 1 kolom (`Unnamed: 0`) dan ditambahkan 3 penanda: `ada_label_luna`, `banner_belum_selesai`, `pulled_tidak_cocok`. |
| Total sel kosong | 0 | 0 | Tidak ada sel kosong sebelum maupun sesudah, dan tidak ada placeholder yang menyamar sebagai data. |
| Baris duplikat | 0 | 0 | Tidak ada duplikat persis, dan `name` unik pada 103 baris. |
| Kategori pada kolom kunci (`region`) | 9 | 9 | Salah ketik diperbaiki, jumlah kategori tetap karena hanya ejaan yang berubah. |

Pada dataset ini tidak ada nilai kosong yang bertambah, karena sejak awal tidak ada placeholder yang menyamar sebagai angka.

## Reproduktibilitas

- [ ] Notebook berjalan dari awal sampai akhir setelah *Restart and run all*
- [ ] Pembersihan dibungkus dalam satu fungsi, bukan tersebar di banyak sel
- [ ] `data/raw/` tidak berubah dan tidak ikut diunggah
- [ ] Hasil tersimpan di `data/processed/`
- [ ] `python cek_m2.py` berjalan tanpa status GAGAL
