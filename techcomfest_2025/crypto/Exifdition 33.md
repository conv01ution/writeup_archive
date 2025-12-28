# Analisis Soal dan Source Code: ExifChall

Diberikan sebuah file `chall.py` yang berjalan di sisi server. 

<img width="430" height="306" alt="Screenshot 2025-12-28 080819" src="https://github.com/user-attachments/assets/3c3c9f80-c427-471b-9f69-f11ac1bb5d94" />

Berikut adalah poin-poin analisis mendalam mengenai mekanisme dan kerentanan kode tersebut.

## 1. Mekanisme Enkripsi

Program ini menggunakan algoritma **Double AES-CBC**. Alur enkripsinya adalah sebagai berikut:

1. Pesan/Plaintext (**P**) diberikan *padding*.
2. Dienkripsi menggunakan **Kunci 1** ().
3. Hasil enkripsi pertama dienkripsi kembali menggunakan **Kunci 2** ().

## 2. Kelemahan Pembangkitan Kunci (Vulnerability)

Celah keamanan utama ditemukan pada fungsi `gen()`, yaitu:

<img width="375" height="250" alt="Screenshot 2025-12-28 081639" src="https://github.com/user-attachments/assets/62946e9a-f8a3-4c12-af04-2c695a273622" />

* **Ruang Kunci Kecil (Small Key Space):** Kunci dibangkitkan hanya dari permutasi string `"GOLDSHIP"` (8 karakter unik). Total kemungkinan permutasi hanya . Meskipun terdapat 4 variabel (), masing-masing variabel hanya memiliki 40.320 kemungkinan.
* **Known Plaintext Attack (KPA):** Program menjalankan perintah `exiftool -j flag.png`. Output standar ExifTool dalam format JSON (Python `str(list_of_dict)`) selalu dimulai dengan struktur tetap: `[{'SourceFile': '`. Ini memberikan kita **16 byte pertama** dari Plaintext () yang sudah diketahui secara pasti.

## 3. Strategi Penyelesaian: Meet-in-the-Middle (MitM)

Karena menggunakan enkripsi ganda, kita dapat memanfaatkan serangan *Meet-in-the-Middle*:

**Langkah-langkah MitM:**

1. **Sisi Enkripsi (Kiri):** Lakukan enkripsi terhadap Plaintext () yang diketahui menggunakan semua 40.320 kemungkinan . Simpan hasilnya ke dalam tabel atau *hash map*.
2. **Sisi Dekripsi (Kanan):** Lakukan dekripsi terhadap Ciphertext () menggunakan semua 40.320 kemungkinan .
3. **Pencocokan:** Cek apakah hasil dekripsi pada langkah ke-2 ada di dalam tabel hasil langkah ke-1. Jika cocok, maka kombinasi kunci telah ditemukan.

> **Catatan:** Karena Python terlalu lambat untuk memproses puluhan ribu operasi AES secara cepat, disarankan menggunakan bahasa **C++**.

## 4. Langkah Pengerjaan

### A. Setup Lingkungan (Docker)

Gunakan Docker untuk memastikan pustaka (*library*) dan lingkungan serupa dengan server target.

**Terminal 1 (Menjalankan Server):**

```bash
sudo docker build -t exifchall .
sudo docker run -p 8303:8010 --rm -it --name my_chall exifchall

```

**Terminal 2 (Akses Environment):**

```bash
sudo docker exec -it my_chall /bin/sh
cd /tmp

```

### B. Membuat Solver

1. Buat program **C++** untuk melakukan *cracking* dengan mencoba semua permutasi `"GOLDSHIP"`.
2. Buat **Script Python** sebagai *wrapper* untuk menangani koneksi *socket* ke server dan mengirimkan data ke program C++.
3. Setelah kunci ditemukan, dekripsi seluruh ciphertext untuk mengambil file gambar.
4. Flag ditemukan di dalam *metadata field* `Comment` pada file `flag.png`.

---

Tentu, berikut adalah penulisan ulang analisis tersebut ke dalam struktur Markdown yang lebih rapi, sistematis, dan mudah dibaca:

---

# Analisis Soal dan Source Code: ExifChall

Diberikan sebuah file `chall.py` yang berjalan di sisi server. Berikut adalah poin-poin analisis mendalam mengenai mekanisme dan kerentanan kode tersebut.

## 1. Mekanisme Enkripsi

Program ini menggunakan algoritma **Double AES-CBC**. Alur enkripsinya adalah sebagai berikut:

1. Pesan/Plaintext (**P**) diberikan *padding*.
2. Dienkripsi menggunakan **Kunci 1** ().
3. Hasil enkripsi pertama dienkripsi kembali menggunakan **Kunci 2** ().

## 2. Kelemahan Pembangkitan Kunci (Vulnerability)

Celah keamanan utama ditemukan pada fungsi `gen()`, yaitu:

* **Ruang Kunci Kecil (Small Key Space):** Kunci dibangkitkan hanya dari permutasi string `"GOLDSHIP"` (8 karakter unik). Total kemungkinan permutasi hanya . Meskipun terdapat 4 variabel (), masing-masing variabel hanya memiliki 40.320 kemungkinan.
* **Known Plaintext Attack (KPA):** Program menjalankan perintah `exiftool -j flag.png`. Output standar ExifTool dalam format JSON (Python `str(list_of_dict)`) selalu dimulai dengan struktur tetap: `[{'SourceFile': '`. Ini memberikan kita **16 byte pertama** dari Plaintext () yang sudah diketahui secara pasti.

## 3. Strategi Penyelesaian: Meet-in-the-Middle (MitM)

Karena menggunakan enkripsi ganda, kita dapat memanfaatkan serangan *Meet-in-the-Middle*:

**Langkah-langkah MitM:**

1. **Sisi Enkripsi (Kiri):** Lakukan enkripsi terhadap Plaintext () yang diketahui menggunakan semua 40.320 kemungkinan . Simpan hasilnya ke dalam tabel atau *hash map*.
2. **Sisi Dekripsi (Kanan):** Lakukan dekripsi terhadap Ciphertext () menggunakan semua 40.320 kemungkinan .
3. **Pencocokan:** Cek apakah hasil dekripsi pada langkah ke-2 ada di dalam tabel hasil langkah ke-1. Jika cocok, maka kombinasi kunci telah ditemukan.

> **Catatan:** Karena Python terlalu lambat untuk memproses puluhan ribu operasi AES secara cepat, disarankan menggunakan bahasa **C++**.

## 4. Langkah Pengerjaan

### A. Setup Lingkungan (Docker)

Gunakan Docker untuk memastikan pustaka (*library*) dan lingkungan serupa dengan server target.

**Terminal 1 (Menjalankan Server):**

```bash
sudo docker build -t exifchall .
sudo docker run -p 8303:8010 --rm -it --name my_chall exifchall

```

**Terminal 2 (Akses Environment):**

```bash
sudo docker exec -it my_chall /bin/sh
cd /tmp

```

### B. Membuat Solver

1. Buat program **C++** untuk melakukan *cracking* dengan mencoba semua permutasi `"GOLDSHIP"`.
2. Buat **Script Python** sebagai *wrapper* untuk menangani koneksi *socket* ke server dan mengirimkan data ke program C++.
3. Setelah kunci ditemukan, dekripsi seluruh ciphertext untuk mengambil file gambar.
4. Flag ditemukan di dalam *metadata field* `Comment` pada file `flag.png`.

---

### **FLAG:**
`TCF{https://youtu.be/I8HYN3WsuOQ?si=vYoKZsTBRICyxiuL}`
