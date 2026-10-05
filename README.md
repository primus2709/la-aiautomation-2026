# Latihan Assessment AIAE: Pembersihan & Analisis Data Bank Indonesia

Notebook Google Colab (`Lat_Assesment_AIAE.ipynb`) untuk membersihkan tiga kumpulan data latihan Bank Indonesia yang masih kotor, menganalisisnya (statistik deskriptif, visualisasi, korelasi, regresi), lalu menyusun hasilnya menjadi laporan Word otomatis.

## Daftar Isi

- [Gambaran Umum](#gambaran-umum)
- [Struktur Data](#struktur-data)
- [Alur Kerja](#alur-kerja)
- [Aturan Pembersihan Data](#aturan-pembersihan-data)
- [Hasil Analisis](#hasil-analisis)
- [Cara Menjalankan](#cara-menjalankan)
- [Keluaran](#keluaran)
- [Catatan & Keterbatasan](#catatan--keterbatasan)

## Gambaran Umum

Input berupa satu berkas Excel, `data_latihan_bi_kotor.xlsx`, berisi tiga sheet dengan berbagai anomali: format tanggal campuran, penulisan teks tidak seragam, angka bertipe teks, nilai negatif, nilai di luar skala, duplikat, dan sel kosong. Notebook ini menyelesaikan tiga tahap:

1. **Pembersihan data** untuk ketiga sheet, disimpan ke `data_bersih_bi.xlsx`.
2. **Analisis** berupa statistik deskriptif, empat grafik, matriks korelasi, dan regresi linear sederhana.
3. **Pelaporan** berupa dokumen Word (`.docx`) berisi tabel, gambar, catatan data, serta temuan dan kesimpulan.

## Struktur Data

| Sheet | Isi | Baris awal | Baris akhir |
|---|---|---|---|
| `Transaksi_BI` | Transaksi per kantor BI, kanal, jenis, kategori, nilai, dan biaya admin | 200 | 194 |
| `Survei_Persepsi` | Survei responden tentang QRIS dan BI-FAST (skor 1-5, frekuensi 1-7) | 150 | 146 |
| `Data_Makro_Inklusi` | Data bulanan 6 provinsi: indeks inklusi keuangan, volume/nilai transaksi QRIS, jumlah merchant, tabungan terhadap PDB | 120 | 120 |

## Alur Kerja

```
data_latihan_bi_kotor.xlsx
        │
        ▼
 Pembersihan 3 sheet ──► data_bersih_bi.xlsx
        │
        ▼
 Analisis & visualisasi ──► grafik_1..4 (.png)
        │
        ▼
 Laporan Word ──► laporan_analisis_bi.docx
                  laporan_analisis_bi_lengkap.docx
```

## Aturan Pembersihan Data

**Sheet 1: Transaksi_BI**
- Tanggal dikonversi dengan mencoba berurutan format `YYYY-MM-DD`, `DD/MM/YYYY`, dan `DD Bulan YYYY`.
- Kolom teks (`Kantor_BI`, `Kanal_Pembayaran`, `Jenis_Transaksi`, `Kategori`): spasi dibuang dan huruf besar/kecil diseragamkan.
- Kolom angka (`Nilai_Transaksi`, `Biaya_Admin`): awalan "Rp" dan titik pemisah ribuan dibuang.
- Duplikat persis dihapus. Nilai negatif dan nilai ekstrem (di luar Q1 − 3×IQR atau Q3 + 3×IQR) dikosongkan.
- Sel kosong diisi **median** (angka) atau **modus** (teks dan tanggal).

**Sheet 2: Survei_Persepsi**
- Nama kolom distandarkan dan duplikat dihapus.
- `Usia`: teks "tahun" dibuang, hanya usia 17-100 yang dianggap valid.
- `Jenis_Kelamin`, `Domisili`, `Pekerjaan`: variasi penulisan dipetakan ke nilai baku (misalnya "jabar" menjadi "Jawa Barat", "L" menjadi "Laki-laki").
- Skor di luar skala dikosongkan (frekuensi QRIS 1-7, lima skor lainnya 1-5).
- Sel kosong diisi median yang dibulatkan (angka) atau modus (teks).

**Sheet 3: Data_Makro_Inklusi**
- Nama kolom distandarkan (spasi dan huruf besar/kecil) dan duplikat dihapus.
- `Bulan` diseragamkan ke tanggal 1 pada bulan terkait dari berbagai format (`MM/YYYY`, `YYYY-MM`, `Mar 2023`, datetime).
- Nama provinsi diseragamkan, dan `Jumlah_Merchant_QRIS` yang bertipe teks diubah menjadi angka.
- Batas wajar: indeks inklusi dan tabungan/PDB 0-100, volume dan nilai transaksi tidak negatif, merchant 0-10.000.000. Nilai di luar batas dikosongkan.
- Sel kosong diisi **median per provinsi**.

## Hasil Analisis

- **Kanal pembayaran:** rata-rata nilai transaksi tertinggi ada pada Transfer BI-FAST (±494,75 juta rupiah) dan terendah pada QRIS (±384,35 juta). QRIS justru kanal dengan jumlah transaksi terbanyak (67 dari 194).
- **Tren QRIS:** rata-rata nilai transaksi QRIS per bulan cenderung naik, dari 274,62 (Jan 2023) ke 406,55 (Agu 2024), dengan puncak 437,30 pada Juli 2024.
- **Korelasi survei:** semua korelasi antar variabel tergolong lemah. Tertinggi adalah Persepsi Keamanan dengan Loyalitas (0,22) dan terendah adalah Usia dengan Persepsi Keamanan (−0,15).
- **Regresi (data makro):** Volume Transaksi QRIS → Nilai Transaksi QRIS memiliki koefisien 0,6355, R² = 0,9404, dan r = 0,9698. Hubungannya sangat kuat, tetapi menunjukkan keterkaitan, bukan sebab-akibat.

## Cara Menjalankan

1. Buka `Lat_Assesment_AIAE.ipynb` di [Google Colab](https://colab.research.google.com/).
2. Unggah `data_latihan_bi_kotor.xlsx` ke sesi Colab (berkas ini **tidak disertakan** di repositori).
3. Jalankan sel secara berurutan dari atas ke bawah (`Runtime > Run all`).
4. Berkas hasil akan diunduh otomatis lewat `google.colab.files.download`.

**Dependensi:** `pandas`, `numpy`, `matplotlib`, `seaborn`, `scikit-learn`, `openpyxl`, `python-docx`. Di Colab semuanya sudah tersedia kecuali `python-docx`, yang dipasang oleh notebook (`!pip install -q python-docx`).

Untuk menjalankan di luar Colab, hapus atau ganti sel yang memakai `google.colab.files`.

## Keluaran

| Berkas | Keterangan |
|---|---|
| `data_bersih_bi.xlsx` | Data bersih, 3 sheet dengan nama yang sama seperti input |
| `grafik_1_kanal.png` | Diagram batang rata-rata nilai transaksi per kanal |
| `grafik_2_tren.png` | Diagram garis tren rata-rata nilai transaksi QRIS per bulan |
| `grafik_3_korelasi.png` | Heatmap korelasi variabel survei |
| `grafik_4_regresi.png` | Scatter plot dan garis regresi volume vs nilai transaksi QRIS |
| `laporan_analisis_bi.docx` | Laporan versi awal (kerangka, tabel, gambar) |
| `laporan_analisis_bi_lengkap.docx` | Laporan lengkap dengan pendahuluan, catatan data, temuan, dan kesimpulan |

## Catatan & Keterbatasan

- **Imputasi median memengaruhi hasil.** Pada `Nilai_Transaksi`, nilai 419.292.000 muncul 10 kali karena berasal dari pengisian median, sehingga rata-rata dan sebaran kolom tersebut perlu dibaca dengan hati-hati.
- Jumlah baris awal (200 dan 150) ditulis langsung (*hardcoded*) pada sel pengecekan, bukan dihitung ulang dari data.
- Aturan pembersihan (rentang usia, batas skala, ambang IQR 3×) adalah keputusan analis untuk latihan ini, bukan standar baku.
- Berkas input harus berada di direktori kerja yang sama dengan notebook, dengan nama sheet persis: `Transaksi_BI`, `Survei_Persepsi`, dan `Data_Makro_Inklusi`.
