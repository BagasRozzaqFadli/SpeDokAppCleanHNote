# Product Requirement Document (PRD)
## CleanHNote - Aplikasi Manajemen Tugas Bersih-bersih

---

### Informasi Tim
- **Nama Kelompok**: Bagas Rozzaq Fadli (221240001280) & Muhammad Alif Nur Hanif (221240001276)
- **Mata Kuliah**: Pemrograman Mobile Lanjut
- **Tanggal**: September 2025

---

## 1. Apa itu CleanHNote?

CleanHNote adalah aplikasi produktivitas berbasis **Flutter** yang dirancang untuk menjembatani kebutuhan manajemen jadwal pribadi dan kolaborasi tim kerja.

Aplikasi ini menerapkan arsitektur *Hybrid Backend* yang menggabungkan **Firebase** (sebagai *core engine* untuk autentikasi, aturan keamanan, dan data real-time) dengan **Appwrite** (sebagai *storage engine* untuk analitik tim dan pengarsipan bukti kerja). Keunikan teknis aplikasi ini terletak pada mekanisme penanganan gambar, di mana bukti foto dikompresi otomatis dan dikonversi menjadi format Base64 untuk efisiensi penyimpanan string database.

## 2. Fitur Aplikasi

### 2.1 Fitur Gratis
- ✅ **Personal Scheduler**: Membuat, mengedit, dan menghapus jadwal pribadi (CRUD) dengan batasan 5 jadwal.
- ✅ **Basic Reminder**: Notifikasi lokal sesuai waktu kegiatan.
- ✅ **Limited Team Join**: Dapat bergabung ke maksimal 3 Tim sebagai anggota.
- ✅ **Status Update**: Menandai tugas pribadi sebagai selesai.

### 2.2 Fitur Premium (Berbayar)
- ✅ **Personal Scheduler**: Membuat, mengedit, dan menghapus jadwal pribadi (CRUD) tanpa batasan jumlah.
- ✅ **Create Team**: Membuat kelompok kerja (Project).
- ✅ **Extended Limit**: Dapat bergabung atau mengelola hingga 15 Tim.
- ✅ **Assign Tasks**: Memberikan tugas spesifik (Assignment) kepada anggota tim berdasarkan ID pengguna.
- ✅ **Photo Proof System**: Mengunggah bukti foto Before dan After.
- ✅ **Monitoring Status**:
  - Status: Pending, Done, Late (Terlambat).
  - Mengetahui kapan tugas dilihat oleh anggota.
  - Mengetahui kapan tugas ditandai terlambat.
- ✅ **Team Analytics**: Statistik kinerja tim yang disimpan dan diolah menggunakan Appwrite.

## 3. Siapa yang Akan Menggunakan?

- **Individu (Pelajar/Umum)**: Menggunakan fitur Personal Task untuk mencatat jadwal harian atau deadline tugas kuliah.
- **Tim Kecil (Kost/Divisi Kantor/Kepanitiaan)**: Menggunakan fitur Team & Assignments untuk membagi tugas piket atau pekerjaan, dengan kewajiban upload bukti foto sebagai validasi.

## 4. Teknologi yang Digunakan

- **Frontend Mobile**: Flutter (Dart).
- **Primary Backend (Firebase)**:
  - **Authentication**: Manajemen User & Sesi Login.
  - **Firestore Database**: Menyimpan data relasional (User, Teams, Tasks), serta menangani logika keamanan (Security Rules) untuk validasi peran (Free vs Premium).
- **Secondary Backend (Appwrite)**:
  - **Database & Storage**: Penyimpanan data historis, arsip analitik tim, dan backup data bukti kerja yang berat.
- **Image Processing**:
  - *Library*: flutter_image_compress

## 5. Model Bisnis

- **Freemium**: Pengguna dapat menggunakan fitur jadwal pribadi selamanya secara gratis.
- **Premium Subscription**:
  - **Harga**: Rp 29.000/bulan (Mock Payment).
  - **Benefit**: Membuka fitur *Create Team*, menaikkan limit join tim dari 3 menjadi 15, dan akses ke analitik tim.
  - **Mekanisme**: Menggunakan simulasi *In-App Purchase* dengan validasi status role: 'premium' dan premiumExpiresAt di database.

## 6. Tujuan Sukses

- 100+ download dalam 1 bulan
- Rating 3.0+ di Play Store
- 15% pengguna upgrade ke Premium

---

