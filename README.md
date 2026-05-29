# Analisis Pola Kriminalitas Chicago 2025
### Perbandingan Algoritma Apriori dan FP-Growth dalam Association Rule Mining

---

## Deskripsi Proyek

Proyek ini menganalisis pola kriminalitas Kota Chicago tahun 2025 menggunakan teknik **Association Rule Mining**. Tujuannya adalah menemukan pola tersembunyi antara jenis kejahatan, lokasi, waktu, dan status penangkapan — sehingga dapat memberikan insight yang berguna bagi penegak hukum dalam mengalokasikan sumber daya dan merancang strategi pencegahan kejahatan.

Dua algoritma dibandingkan secara langsung: **Apriori** dan **FP-Growth**, dari sisi kecepatan, penggunaan memori, dan kualitas rules yang dihasilkan.

---

## 📂 Dataset

| Atribut | Detail |
|---|---|
| Sumber | Chicago Police Department — [data.cityofchicago.org](https://data.cityofchicago.org) |
| Periode | April 2025 |
| Jumlah kolom | 22 kolom |
| Kolom yang digunakan | `primary_type`, `location_description`, `arrest`, `community_area`, `date` |

---

## Library yang Digunakan

| Library | Versi | Fungsi |
|---|---|---|
| `pandas` | latest | Manipulasi dan preprocessing data |
| `numpy` | latest | Operasi numerik |
| `matplotlib` | latest | Visualisasi data |
| `seaborn` | latest | Visualisasi distribusi & EDA |
| `mlxtend` | latest | Algoritma Apriori, FP-Growth, dan Association Rules |
| `scikit-learn` | latest | MinMaxScaler (preprocessing tambahan) |
| `tracemalloc` | built-in | Monitoring penggunaan memori |
| `time` | built-in | Pengukuran runtime algoritma |

---

## Langkah Pengerjaan

### 1. Pemahaman Data (Data Understanding)
- Load dataset CSV kriminalitas Chicago 2025
- Eksplorasi 22 kolom dan memahami makna tiap fitur
- Cek duplikat dan missing values

### 2. Preprocessing
- Seleksi kolom relevan: `primary_type`, `location_description`, `arrest`, `community_area`, `date`
- Ekstraksi fitur waktu dari kolom `date`:
  - **Bulan** → ditiadakan (hanya April, tidak variatif)
  - **Hari** → `Weekday` / `Weekend`
  - **Minggu ke-** → `Minggu_1` hingga `Minggu_4`
  - **Jam** → `Dini_Hari` / `Pagi` / `Siang` / `Malam`
- Binning `community_area` → `Area_Utara`, `Area_Tengah`, `Area_Selatan`, `Area_Lainnya`
- Encode `arrest` → `ARREST_YES` / `ARREST_NO`
- Handle missing values menggunakan **modus** per kolom

### 3. Exploratory Data Analysis (EDA)
- Visualisasi distribusi tiap fitur (countplot)
- Identifikasi jenis kejahatan, lokasi, dan waktu yang paling sering muncul

### 4. Pembentukan Transaksi
- Setiap baris data dijadikan satu transaksi (list of items)
- Encoding one-hot menggunakan `TransactionEncoder` dari mlxtend

### 5. Mining — Apriori vs FP-Growth
- Parameter: `min_support = 0.03`, `min_confidence = 0.7`
- Kedua algoritma dijalankan dengan pengukuran **runtime** dan **memori** menggunakan `tracemalloc`
- Generate association rules dengan filter `lift > 1`

### 6. Deduplikasi Rules
- Rules redundan dihapus berdasarkan kesamaan semua item (antecedent + consequent)
- Filter lanjutan: rules dengan inti yang sama (mengabaikan item waktu) hanya diambil yang lift-nya tertinggi
- Hasil: **351 rules → 36 rules unik bermakna**

### 7. Visualisasi
- Scatter plot: Support vs Confidence (warna = Lift)
- Bar chart: Top 10 Rules by Lift
- Bar chart: Perbandingan runtime Apriori vs FP-Growth

---

## 📊 Hasil

### Perbandingan Algoritma

| Metrik | Apriori | FP-Growth |
|---|---|---|
| Frequent Itemsets | sama | sama |
| Jumlah Rules | 351 | 351 |
| Rules setelah dedup | 202 | 36 (filtered) |
| Hasil rules | Identik | Identik |
| Kecepatan | Lebih lambat | **Lebih cepat** |
| Penggunaan memori | Lebih boros | **Lebih efisien** |

> Kedua algoritma menghasilkan rules yang **identik** — membuktikan konsistensi antar metode. FP-Growth unggul dalam efisiensi komputasi.

---

### Top Rules yang Ditemukan

| Rule | Confidence | Lift | Insight |
|---|---|---|---|
| `NARCOTICS → ARREST_YES, Weekday` | 89.5% | **5.70** | Kasus narkotika hampir pasti berujung penangkapan di hari kerja |
| `MOTOR VEHICLE THEFT, Minggu_4 → ARREST_NO, STREET` | 75.0% | 3.47 | Pencurian kendaraan akhir bulan terjadi di jalanan & pelaku bebas |
| `SMALL RETAIL STORE → THEFT, Weekday` | 76.9% | 3.13 | Toko ritel kecil rentan pencurian di hari kerja |
| `ROBBERY, Weekday → ARREST_NO` | **100%** | 1.19 | Seluruh kasus perampokan weekday tidak berujung penangkapan |
| `THEFT, STREET → ARREST_NO, Weekday` | **100%** | 1.19 | Pencurian di jalanan weekday: 0% penangkapan |

---

### Kesimpulan

1. **Narkotika** adalah satu-satunya kejahatan dengan tingkat penangkapan sangat tinggi (>89%), mencerminkan efektivitas operasi khusus narkotika.
2. **Pencurian kendaraan** dominan terjadi di jalanan pada minggu ke-4 bulan dengan tingkat penangkapan sangat rendah — perlu peningkatan patroli akhir bulan.
3. **Perampokan dan pencurian** di jalanan pada hari kerja memiliki tingkat penangkapan **0%** — temuan kritis untuk evaluasi strategi patroli.
4. **Toko ritel kecil** perlu sistem keamanan lebih baik, terutama di hari kerja.
5. **Dini hari di Area Tengah Chicago** secara konsisten menjadi kondisi berisiko tinggi dengan minim penangkapan.

---
