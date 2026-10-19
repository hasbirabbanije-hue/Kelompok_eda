# Cleaning Log

Dataset: transaksi_koperasi_2025.csv  
Baris mentah: 1543 -> baris bersih: 1493  

| # | Langkah | Baris terdampak | Alasan |
|---|---|---|---|
| 1 | Konversi total_bayar ke angka | 250 | Buang "Rp" dan titik pemisah ribuan agar kolom dapat dihitung; titik di sini ribuan, bukan desimal. Gagal konversi: 0 baris. |
| 2 | Konversi tanggal per format | 1.543 | Dua format (1.236 ISO, 307 dd/mm/yyyy) diurai terpisah. format="mixed" dengan dayfirst menukar hari dan bulan pada 483 baris ISO tanpa galat. Gagal: 0; rentang 1 Agt–28 Nov 2025. |
| 3 | Buang spasi awal dan akhir kolom teks | 160 | Spasi tepi pada id_anggota (65), kategori (59), dan metode_bayar (36) memecah groupby dan menggembungkan nunique (id_anggota 318 → 259). |
| 4 | Placeholder teks menjadi kosong | 19 | "-" (13) dan "tidak diketahui" (6) pada metode_bayar bukan metode pembayaran; dijadikan kosong, tidak ditebak. |
| 5 | Placeholder -999 pada rating menjadi kosong | 49 | Angka -999 menyeret rata-rata rating menjadi −27,7 dan tidak terdeteksi isna(). |
| 6 | Seragamkan kategori (serta metode_bayar dan kasir) | 176 | Huruf dan spasi oleh kode; "Alat Tulis" → ATK dan "Minumam" → Minuman diputuskan manual (Lampiran B). kategori 18 tulisan → 5; metode_bayar menyentuh 95 baris, kasir 19 baris. |
| 7 | Hapus duplikat persis | 39 | Seluruh kolom identik; satu baris disimpan. 24 terdeteksi pada data mentah, 15 lain baru identik setelah teks diseragamkan. |
| 8 | Satukan duplikat menurut id_transaksi | 15 | Satu transaksi satu baris. 15 baris terlibat: 4 pasang ID kembar (beda hanya pada catatan_kasir; baris bercatatan disimpan) dan 7 baris ber-id kosong yang dianggap kembar oleh pandas. 10 baris dibuang (4 + 6 baris tanpa id). |
| 9 | Buang baris tanpa id_transaksi | 1 | Kunci kosong tidak dapat ditelusuri ke sumber. Satu baris tersisa dibuang di sini; seluruh 7 baris tanpa id kini keluar. |
| 10 | jumlah ≤ 0 menjadi kosong | 29 | Belum jelas retur atau salah input; barisnya masih berguna untuk kolom lain. |
| 11 | Diskon di luar 0–100 menjadi kosong | 9 | −20 (6 baris), 110 (1), 150 (2) mustahil; dikosongkan menunggu konfirmasi pengurus. |
| 12 | Tandai total yang tidak cocok | 50 | Ditandai (total_mencurigakan), tidak ditimpa, karena belum diketahui kolom mana yang keliru. Selisih berpola ×10 (20), ×0,5 (18), ×2 (12). |
| 13 | saldo_simpanan dibiarkan kosong | 156 | Diduga MNAR; imputasi apa pun membuat rata-rata saldo tampak lebih kecil dari kenyataan. Keterbatasan dilaporkan di Bagian 6. |
| 14 | Tandai keberlakuan berat_kg | 290 | Kosong pada Pulsa dan Jasa karena tidak berlaku, bukan hilang; diberi penanda berlaku_berat, tidak diisi. 51 baris ATK kosong lainnya tidak dapat dipulihkan. |
| 15 | Tandai keberadaan catatan kasir | 1.004 | Kosong berarti kasir tidak menulis catatan; dijadikan penanda biner ada_catatan. |
