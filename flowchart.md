# Flowchart Sistem King Gitar

Dokumen ini berisi diagram alur (Flowchart) dari fitur-fitur utama di dalam sistem **King Gitar**. Diagram digambarkan menggunakan sintaks Mermaid.

---

## 📖 Panduan Simbol Flowchart

Berikut adalah jenis-jenis simbol dasar yang digunakan dalam flowchart ini beserta maknanya:

1. **Terminal / Terminator (Bentuk Oval / Kapsul)**
   - Simbol: `([ Teks ])`
   - Fungsi: Menunjukkan titik **AWAL (Mulai)** atau **AKHIR (Selesai)** dari sebuah alur program.
2. **Process / Proses (Bentuk Persegi Panjang)**
   - Simbol: `[ Teks ]`
   - Fungsi: Menunjukkan suatu **proses, perhitungan, atau aksi** yang dilakukan oleh sistem atau user.
3. **Data / Input-Output (Bentuk Jajar Genjang)**
   - Simbol: `[/ Teks /]`
   - Fungsi: Menunjukkan operasi **input** (memasukkan data) atau **output** (menampilkan hasil).
4. **Decision / Keputusan (Bentuk Belah Ketupat / Diamond)**
   - Simbol: `{ Teks }`
   - Fungsi: Menunjukkan titik **percabangan atau evaluasi kondisi** (Biasanya memiliki jawaban Ya / Tidak).
5. **Database / Penyimpanan (Bentuk Tabung / Silinder)**
   - Simbol: `[( Teks )]`
   - Fungsi: Menunjukkan proses mengambil atau menyimpan data ke dalam **Database**.

---

## 1. Alur Pemesanan / Pembelian Produk (Pengunjung Umum)
Alur ketika pengunjung website melihat katalog produk dan melakukan pemesanan via WhatsApp.

```mermaid
flowchart TD
    %% Mendefinisikan class untuk warna
    classDef terminator fill:#e2e8f0,stroke:#64748b,stroke-width:2px;
    classDef process fill:#bfdbfe,stroke:#3b82f6;
    classDef io fill:#fef08a,stroke:#ca8a04;
    classDef decision fill:#fbcfe8,stroke:#db2777;
    classDef db fill:#bbf7d0,stroke:#22c55e;

    A([Mulai]) :::terminator --> B
    B[/Buka Website King Gitar/] :::io --> C
    C[Akses Halaman Katalog] :::process --> D
    D[/Input Kata Kunci / Filter Kategori/] :::io --> E
    E[(Ambil Data dari Database / Cache)] :::db --> F
    F{Apakah Produk Ditemukan?} :::decision
    
    F -- Ya --> G[/Tampilkan List Produk/] :::io
    F -- Tidak --> H[/Tampilkan 'Produk Kosong'/] :::io
    H --> D
    
    G --> I[Pilih & Klik Detail Produk] :::process
    I --> J[Klik Tombol 'Pesan WhatsApp'] :::process
    J --> K[Sistem Generate Pesan Otomatis] :::process
    K --> L[/Diarahkan ke Chat WhatsApp Admin/] :::io
    L --> M([Selesai]) :::terminator
```

---

## 2. Alur Interaksi Chatbot AI (King Gitar AI)
Alur ketika pengunjung menggunakan fitur live chat AI untuk bertanya atau meminta rekomendasi.

```mermaid
flowchart TD
    A([Mulai]) --> B[/User Membuka Widget Chatbot/] 
    B --> C[/Input Pertanyaan di Textbox/]
    C --> D[Klik Tombol Kirim / Enter]
    D --> E[Aplikasi Meneruskan Pesan ke API Backend]
    E --> F[(Ambil Konteks: Data Toko & Produk dari Database)]
    F --> G[Susun Payload beserta Riwayat Chat]
    G --> H[Kirim Request ke Groq API (Model Llama-3)]
    H --> I{Apakah Berhasil (Status 200)?}
    
    I -- Ya --> J[Ekstrak Jawaban dari Groq]
    J --> K[/Tampilkan Jawaban dengan Efek Ketik (Typewriter)/]
    
    I -- Tidak --> L[/Tampilkan Peringatan Error / Gagal Terhubung/]
    
    K --> M([Selesai / Menunggu Pertanyaan Berikutnya])
    L --> M
```

---

## 3. Alur Autentikasi Admin (Login via Google OAuth)
Alur ketika seorang admin mencoba masuk (login) ke dalam sistem panel dashboard (CMS) menggunakan akun Google.

```mermaid
flowchart TD
    A([Mulai]) --> B[/Akses Halaman Login (/login)/]
    B --> C[Klik Tombol 'Login with Google']
    C --> D[Redirect User ke Server Google]
    D --> E[/User Memilih Akun Google/]
    E --> F[Google Mengirim Callback ke Sistem]
    F --> G{Pengecekan: Apakah Email Terdaftar?}
    
    G -- Tidak Terdaftar --> H[/Tolak Akses & Redirect kembali ke Login/]
    H --> Z
    
    G -- Terdaftar --> I{Pengecekan: Apakah User is_admin = 1?}
    
    I -- Bukan Admin --> H
    
    I -- Admin --> J[Buat API Token (Sanctum)]
    J --> K[Simpan Session Login]
    K --> L[/Buka Halaman Admin Dashboard/]
    L --> Z([Selesai])
```

---

## 4. Alur Manajemen Data Admin (Contoh: Menambah Produk)
Alur ketika admin berhasil login dan melakukan proses tambah data produk baru beserta gambar ke dalam database. Ini juga berlaku mirip untuk mengelola kategori atau banner.

```mermaid
flowchart TD
    A([Mulai]) --> B[/Buka Menu Kelola Produk/]
    B --> C[Klik Tombol 'Tambah Produk']
    C --> D[/Muncul Form Input Produk/]
    D --> E[/Input Nama, Harga, Deskripsi, Kategori, Foto/]
    E --> |Submit Form| F[Backend Memproses Request API]
    F --> G{Apakah Validasi Lolos?}
    
    G -- Tidak Lolos / Data Kosong --> H[/Kembalikan Pesan Error Validasi (422)/]
    H --> D
    
    G -- Lolos Validasi --> I[Upload Foto ke Storage Disk Publik]
    I --> J[Generate Slug Otomatis]
    J --> K[(Simpan Record Baru ke Tabel Products)]
    K --> L[(Catat Aktivitas di Activity Logs)]
    L --> M[(Hapus Cache 'products_index' -> Invalidasi)]
    M --> N[/Tampilkan Notifikasi 'Berhasil' & Tutup Form/]
    N --> O([Selesai])
```
