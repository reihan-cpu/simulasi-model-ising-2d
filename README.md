# Simulasi Model Ising 2D Menggunakan Algoritma Metropolis

Repository ini dibuat sebagai tugas mata kuliah **Fisika Komputasi**. Proyek ini mengimplementasikan **Model Ising 2 Dimensi (2D)** menggunakan **Algoritma Metropolis (Monte Carlo)** untuk mempelajari fenomena **transisi fasa**, **magnetisasi spontan**, dan perubahan perilaku sistem pada berbagai kondisi suhu.

## Link Visit live
(https://reihan01241020.github.io/simulasi-model-ising-2d/)

---

## Anggota Kelompok

| No | Nama | NIM |
|----|----------------------------------|----------|
| 1 | Feliks Salvador Mora Samosir | 01241003 |
| 2 | Rehan Triandoko | 01241020 |
| 3 | Ramadhan Agda Linuwih | 01231014 |

---

## Studi Kasus

### Kasus 1 – Fase Feromagnetik (T = 1.0)
Pada suhu rendah (**T < Tc**), spin cenderung sejajar sehingga terbentuk domain magnetik yang besar. Nilai magnetisasi rata-rata mendekati ±1 yang menunjukkan fase feromagnetik.

### Kasus 2 – Transisi Fasa dan Kritikalitas (T = 2.27)
Pada suhu kritis (**Tc ≈ 2.27**), sistem mengalami transisi fasa. Domain magnetik menjadi tidak stabil dan magnetisasi mengalami fluktuasi yang besar.

### Kasus 3 – Fase Paramagnetik (T = 4.0)
Pada suhu tinggi (**T > Tc**), energi termal mendominasi sehingga arah spin menjadi acak. Magnetisasi rata-rata mendekati nol yang menunjukkan fase paramagnetik.

---

## Struktur Repository
text
/ (root repository)
├── README.md
└── docs/
    ├── index.html
    ├── Kasus_1_Suhu_Rendah.html
    ├── Kasus_2_Suhu_Sedang.html
    └── Kasus_3_Suhu_Tinggi.html
├── notebooks/
│   ├── Kasus_1_Suhu_Rendah.ipynb
│   ├── Kasus_2_Suhu_Sedang.ipynb
│   └── Kasus_3_Suhu_Tinggi.ipynb
│
└── README.md

---

# Simulasi Model Ising 2D Menggunakan Algoritma Metropolis

Penilaian terhadap hasil modifikasi dan pengerjaan tugas ini didasarkan pada aspek-aspek berikut. Keseluruhan kriteria telah terpenuhi di dalam simulasi ini dengan rincian sebagai berikut:

### 1. Akurasi Matematis dan Fisis (Terpenuhi)
**Kriteria:** Ketepatan formulasi matematis dalam menghitung selisih energi lokal $\Delta E = 2s_{i,j} \sum s_{\text{tetangga}}$ dengan menerapkan kondisi batas periodik (*periodic boundary conditions*) menggunakan operasi modulo, serta kebenaran implementasi probabilitas penerimaan Metropolis $\mathcal{R} = e^{-\Delta E / T}$.

**Implementasi pada Kode:**
* **Kondisi Batas Periodik (PBC) dengan Modulo:** Pencarian spin tetangga (atas, bawah, kanan, kiri) sudah menggunakan operasi modulo `% N` agar ujung kisi menyambung ke ujung lainnya tanpa menimbulkan *edge effect*.
    ```python
    neighbors = (grid[(i + 1) % N, j] + 
                 grid[(i - 1) % N, j] + 
                 grid[i, (j + 1) % N] + 
                 grid[i, (j - 1) % N])
    ```
* **Formulasi Selisih Energi Lokal ($\Delta E$):** Kode menghitung perubahan energi hanya pada spin yang dipilih, secara persis mengikuti rumus $\Delta E = 2s_{i,j} \sum s_{\text{tetangga}}$.
    ```python
    dE = 2 * spin * neighbors
    ```
* **Probabilitas Penerimaan Metropolis:** Syarat penerimaan $\mathcal{R} = e^{-\Delta E / T}$ telah diterapkan dengan presisi pada kriteria penerimaan keadaan makrostate yang baru.
    ```python
    if dE <= 0 or np.random.rand() < exp_dE[dE]:
        grid[i, j] *= -1
    ```

### 2. Efisiensi Komputasi (Terpenuhi)
**Kriteria:** Kebersihan struktur kode Python, penulisan dokumentasi/komentar yang informatif, serta optimalisasi algoritma dengan memanfaatkan perhitungan energi lokal alih-alih menghitung ulang energi total seluruh kisi pada setiap iterasi.

**Implementasi pada Kode:**
* **Optimasi Energi Lokal:** Program dioptimasi secara maksimal dengan tidak menghitung ulang energi total sistem (Hamiltonian, $H$) pada setiap iterasi, melainkan hanya menghitung fluktuasi perubahan energi lokal ($\Delta E$).
* **Optimasi Tingkat Lanjut (Pre-calculation):** Daripada menghitung ulang fungsi eksponensial $e^{-\Delta E / T}$ sebanyak 200.000 kali di dalam *loop*, kode menghitungnya di awal karena kemungkinan nilai $\Delta E$ pada kisi 2D (dengan $J=1$) hanya ada lima macam ($-8, -4, 0, 4, 8$). Nilai probabilitas termal ini disimpan di dalam struktur data *dictionary* untuk mempercepat komputasi secara signifikan.
    ```python
    exp_dE = {dE: np.exp(-dE / T) for dE in [-8, -4, 0, 4, 8]}
    ```

### 3. Komunikasi Ilmiah (Terpenuhi)
**Kriteria:** Kejelasan visualisasi data yang disajikan (kelengkapan label sumbu, judul grafik, dan skala yang tepat) serta kedalaman analisis teoretis fisis yang dituliskan pada penjelasan teks.

**Implementasi pada Laporan & Visualisasi:**
* **Kejelasan Visualisasi Data:** Plot grafik yang dihasilkan menggunakan modul `matplotlib` disajikan mengikuti standar publikasi ilmiah, yang mencakup:
    * Judul grafik yang representatif (`set_title`).
    * Label sumbu X dan Y yang eksplisit (Langkah Monte Carlo vs Magnetisasi).
    * Skala sumbu Y dikunci statis (`set_ylim(-1.1, 1.1)`) agar pengamat dapat membandingkan amplitudo fluktuasi magnetisasi secara objektif pada masing-masing kondisi suhu.
    * Peta konfigurasi kisi 2D divisualisasikan menggunakan peta warna biner (`cmap='binary'`) yang lazim digunakan dalam literatur Model Ising.
* **Kedalaman Analisis Teoretis:** Setiap kasus dalam laporan ini dilengkapi dengan sel *Markdown* yang berisi interpretasi fisis termodinamika secara mendalam. Analisis tersebut membahas perilaku fisika statistik sistem, di antaranya: kompetisi antara entropi dan energi internal, fenomena pemecahan simetri spontan (*spontaneous symmetry breaking*), formasi pola domain fraktal di titik kritis, fluktuasi eksitasi termal, hingga keruntuhan keteraturan jarak jauh (*long-range order*).

---

## Referensi dan Sumber Data
1. **Landau, D. P., & Binder, K.** (2014). *A Guide to Monte Carlo Simulations in Statistical Physics* (Bab 4: Ising Model and the Metropolis Algorithm). Cambridge University Press.
2. **Newman, M. E. J., & Barkema, G. T.** (1999). *Monte Carlo Methods in Statistical Physics* (Bab 3 & 4: The Ising Model & Markov Chain Monte Carlo). Clarendon Press.
3. **Rifani, Agus.** Modul Pembelajaran: Simulasi Model Magnet dan Transisi Fasa. (Bahan ajar / Modul perkuliahan).

---

## Teknologi dan Aplikasi yang Digunakan
* **Google Colab:** Sebagai lingkungan pemrograman Python berbasis *cloud*.
* **GitHub & GitHub Pages:** Untuk menyimpan *source code*, mengelola versi proyek, dan mempublikasikan hasil simulasi dalam bentuk website.
* **Python:** Bahasa pemrograman utama.
* **NumPy:** Digunakan untuk komputasi numerik, pembuatan kisi *array*, dan kalkulasi matematis yang efisien pada simulasi.
* **Matplotlib:** Digunakan untuk visualisasi hasil makrostate, konfigurasi spin (kisi 2D), dan grafik kurva magnetisasi.

---

## Penggunaan AI
Pada pengerjaan tugas ini, kelompok kami menggunakan beberapa teknologi AI sebagai berikut:
* **Gemini AI:** Digunakan untuk mendesain struktur instruksi tugas serta melakukan optimasi sintaksis algoritma Metropolis pada potongan kode simulasi.
* **Claude:** Digunakan sebagai mitra diskusi dan referensi tambahan dalam memvalidasi interpretasi fisis mekanika statistik (seperti analisis fluktuasi kritis dan runtuhnya keteraturan jarak jauh), serta merapikan struktur bahasa pada laporan agar sesuai dengan kaidah penulisan komunikasi ilmiah.

---

## Hasil Akhir
Website hasil simulasi dapat diakses melalui GitHub Pages yang berisi:
* [Halaman utama proyek](index.html)
* [Simulasi Kasus 1 (T = 1.0)](Kasus_1_Suhu_Rendah.html)
* [Simulasi Kasus 2 (T = 2.27)](Kasus_2_Suhu_Sedang.html)
* [Simulasi Kasus 3 (T = 4.0)](Kasus_3_Suhu_Tinggi.html)
