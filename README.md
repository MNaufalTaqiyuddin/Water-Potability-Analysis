# Water-Potability-Analysis

## Project Overiview
* Latar Belakang : Memprediksi kelayakan air minum secara otomatis menggunakan Machine Learning berdasar indikator kualitas fisik dan kimia pada air.
* Tujuan proyek ini adalah membangun **Sistem Peringatan Dini (Early Warning System)** bagi pengelola fasilitas pengolahan air untuk identidikasi air yang berpotensi terkontaminasi sebelum didiistribusikan ke konsumen.

## Problem & Business Context
* **Tantangan :** Pengujian sampel air secara manual di laboratorium membutuhkan waktu dan biaya operasional.
* **Risiko Bisnis atau Kesehatan :**
  *  **False Positive (FP) :** Air tidak layak tapi terprediksi layak -> Merupakan risiko utama dan ada ancaman kesehatan di masyarakat da tuntutan hukum.
  *  **False Negative (FN) :** Air layak tapi terprediksi tidak layak -> Merupakan risiko operasional dapat menimbulkan pemborosan biaya untuk pengolahan ulang
* **Goal Project :** Membangun model klasifikasi yang dapat menyeimbangkan ketepatan prediksi (precision) dan sensitivitas (recall) pada data yang memiliki class imbalance

## Data Pipeline & Methodology
1. **Data Preprocessing & Cleaning:**
   * Penanganan *missing values* pada kolom `ph`, `Sulfate`, dan `Trihalomethanes` menggunakan nilai **median berbasis kelompok target** (`groupby Potability`).
   * Pemeriksaan duplikasi data dan pengecekan sebaran *outlier*.
2. **Handling Class Imbalance:**
   * Menerapkan **SMOTE (Synthetic Minority Over-sampling Technique)** *hanya* pada **Data Training** pasca *train-test split* (80:20) untuk mencegah *Data Leakage*.
3. **Model Training & Comparison:**
   * Membandingkan 3 algoritma: *Logistic Regression*, *Random Forest*, dan *XGBoost*.
   * Menggunakan *StandardScaler* khusus untuk model sensitif skala (*Logistic Regression*).

## Model Evaluation & Results

### 1. Perbandingan Performa Model
Evaluasi dilakukan pada **Data Test** murni (tanpa SMOTE):

| Model | Accuracy | Precision | Recall | F1-Score |
| :--- | :---: | :---: | :---: | :---: |
| Logistic Regression | 52.1% | 40.8% | 50.0% | 0.449 |
| Random Forest | **65.1%** | 56.4% | 46.5% | 0.510 |
| **XGBoost (Selected)** | 62.3% | **51.8%** | **52.0%** | **0.519** |

> **Alasan Pemilihan Model:** **XGBoost** dipilih sebagai model akhir karena memiliki keseimbangan *Precision* dan *Recall* terbaik (serta **F1-Score tertinggi: 0.519**), sehingga prediksi kelayakan air lebih stabil dibanding model lain yang terbias ke salah satu kelas.

### 2. Confusion Matrix Analysis (XGBoost)
<img width="539" height="455" alt="confusion_matrix" src="https://github.com/user-attachments/assets/6fb935cd-6fc8-4732-a62e-76f8184fb4e5" />

* **True Negative (276):** Berhasil mendeteksi air tidak layak minum secara akurat.
* **True Positive (133):** Berhasil mengidentifikasi air bersih yang aman diminum.
* **False Positive (124) & False Negative (123):** Menunjukkan bahwa model lebih optimal digunakan sebagai *screen filter* tahap awal pendamping analisis laboratorium.

### 3. Key Feature Importances
<img width="919" height="547" alt="importances" src="https://github.com/user-attachments/assets/1ab6037a-4eee-41cd-83f5-677df054723b" />

Tiga variabel paling dominan yang memengaruhi keputusan model:
1. **Sulfate ($\approx 14\%$):** Kadar sulfat berlebih berdampak pada rasa dan tingkat toksisitas air.
2. **pH ($\approx 13\%$):** Indikator keasaman utama (standar aman 6.5 – 8.5).
3. **Chloramines ($\approx 13\%$):** Sisa senyawa desinfektan kimia.

## Business Recommendations
1. **Prioritas Alokasi Sensor:** Pihak pengelola disarankan mengalokasikan anggaran untuk pemasangan sensor *real-time* otomatis pada variabel **pH** dan **Sulfate**, karena dua fitur ini paling sensitif menentukan kelayakan air.
2. **Standard Operating Procedure (SOP):** Diterapkan sistem *flagging* otomatis jika nilai Sulfat atau pH melewati batas toleransi WHO sebelum air diproses oleh model Machine Learning.
