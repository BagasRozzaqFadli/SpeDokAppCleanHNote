# Software Requirement Specification (SRS)
## CleanHNote - Spesifikasi Kebutuhan Perangkat Lunak

---

### Informasi Tim
- **Nama Kelompok**: Bagas Rozzaq Fadli (221240001280) & Muhammad Alif Nur Hanif (221240001276)
- **Mata Kuliah**: Pemrograman Mobile Lanjut
- **Tanggal**: September 2025

---

## 1. Pengenalan

**CleanHNote** adalah aplikasi produktivitas berbasis Flutter yang telah berevolusi dari sekadar pencatat tugas kebersihan menjadi aplikasi manajemen penjadwalan komprehensif (Personal & Team).
Aplikasi ini menggunakan arsitektur **Hybrid Backend**:
1. **Firebase (Core)**: Menangani Autentikasi, Database Real-time (Firestore), dan Security Rules.
2. **Appwrite (Secondary)**: Menangani penyimpanan arsip analitik tim dan backup data berat.
3. **Image Processing**: Menggunakan mekanisme kompresi *client-side* dan penyimpanan gambar dalam format *Base64 String* untuk efisiensi transfer data.

## 2. Spesifikasi Fungsional (Functional Requirements)

### 2.1 Modul Autentikasi & Akun (Firebase Auth)
- **REQ-AUTH-001**: Pengguna dapat mendaftar (Register) dan Login menggunakan Email & Password.
- **REQ-AUTH-002**: Sistem harus membedakan peran pengguna (Role) menjadi: free, premium, dan admin.
- **REQ-AUTH-003 (Mock Payment)**: Pengguna dapat melakukan simulasi upgrade ke akun Premium. Sistem akan mengubah status role di Firestore dan mencatat premiumExpiresAt.

### 2.2 Modul Jadwal Personal (Gratis & Premium)
- **REQ-PERS-001**: Pengguna dapat membuat jadwal pribadi (Judul, Tanggal, Jam).
- **REQ-PERS-002**: Pengguna dapat melihat daftar jadwal pribadi (Read) yang diurutkan berdasarkan waktu terdekat.
- **REQ-PERS-003**: Pengguna dapat mengubah status tugas menjadi "Selesai".
- **REQ-PERS-004**: Pengguna TIDAK DAPAT melihat atau mengakses jadwal pribadi milik pengguna lain (Diatur oleh Security Rules).

### 2.3 Modul Tim & Kolaborasi (Fitur Premium)
- **REQ-TEAM-001 (Create)**: Pengguna dengan role premium dapat membuat Tim baru.
- **REQ-TEAM-002 (Join)**: Pengguna dapat bergabung ke tim menggunakan Invite Code atau Team ID.
- **REQ-TEAM-003 (Consistency Check)**: Sistem harus memvalidasi apakah pengguna masih terdaftar sebagai anggota (memberIds) sebelum mengizinkan akses ke tugas tim.
- **REQ-TEAM-004 (Kick/Leave)**: Pemilik tim (Owner) dapat mengeluarkan anggota, dan anggota dapat keluar sendiri dari tim.

### 2.4 Modul Penugasan & Bukti Foto (Base64 System)
- **REQ-TASK-001 (Assign)**: Owner tim dapat menunjuk anggota spesifik (assignedToUid) untuk mengerjakan tugas.
- **REQ-TASK-002 (Capture & Compress)**: Aplikasi harus dapat mengambil foto dari kamera, lalu secara otomatis mengompresi ukuran file (Target < 500KB).
- **REQ-TASK-003 (Encode)**: Aplikasi harus mengonversi hasil kompresi gambar menjadi Base64 String.
- **REQ-TASK-004 (Upload)**: Anggota tim mengunggah string Base64 tersebut ke field photoBeforeBase64 atau photoAfterBase64 di Firestore.
- **REQ-TASK-005 (Late Mark)**: Jika tugas diselesaikan melewati tenggat waktu, sistem otomatis menandai status late dan mencatat lateMarkedAt.

### 2.5 Modul Analitik (Hybrid Integration)
- **REQ-ANA-001**: Sistem mengirimkan ringkasan penyelesaian tugas tim ke Appwrite Database untuk pengolahan data statistik.

## 3. Kebutuhan Non-Fungsional

### 3.1 Performa (Performance)
- **NFR-PER-01**: Proses kompresi gambar hingga konversi Base64 harus selesai di bawah 3 detik pada perangkat <i>mid-range</i>.
- **NFR-PER-02**: Ukuran payload data (String Base64) dibatasi maksimal 1MB per dokumen untuk mematuhi batasan dokumen Firestore.
- **NFR-PER-03**: *Load Time* daftar tugas harus di bawah 2 detik menggunakan *Firestore Pagination* atau *Stream*.

### 3.2 Keamanan (Security - Based on Rules)
- **NFR-SEC-01 (Anti-Escalation)**: Security Rules harus menolak segala upaya pengguna untuk mengubah role mereka sendiri menjadi admin melalui API/Injection.
- **NFR-SEC-02 (Field Protection)**: Anggota tim (Member) hanya diizinkan mengubah field status dan photo, tidak boleh mengubah title atau deadline tugas tim.
- **NFR-SEC-03 (Data Isolation)**: Data jadwal pribadi diisolasi total per userId.

### 3.3 Kompatibilitas
- Mendukung Android (min SDK 24 / Android 7.0) karena kebutuhan library kompresi gambar terbaru.

## 4. Batasan Sistem (System Constraints)

|ParameterAkun|FreeAkun|PremiumMax|
|:-:|---|---|
|Max Join Team|3 Tim|15 Tim|
|Create Team|Tidak Bisa|3 Tim|
|Personal Task|5 Jadwal|Unlimited|

## 5. Skenario Penggunaan (User Stories) 

### 5.1 Skenario: Upgrade & Buat Tim (Premium User)
1. Budi (User Free) masuk ke halaman Profil.
2. Budi menekan tombol "Upgrade Premium" (Simulasi Payment).
3. Sistem mengubah role Budi jadi premium.
4. Budi membuat tim "Kebersihan Kost A".
5. Budi menyalin ID Tim dan mengirimkannya ke grup WhatsApp kost.

### 5.2 Skenario: Mengerjakan Tugas Tim (Member)
1. Ali (Member) membuka notifikasi "Giliran Kamu Menyapu".
2. Ali membuka detail tugas, lalu menekan tombol kamera.
3. Ali memotret lantai yang kotor (Before).
4. Sistem (Background): Resize foto -> Convert to Base64 -> Upload string ke Firestore.
5. Ali menyapu lantai, lalu memotret lagi (After).
6. Ali menekan "Selesai". Status tugas berubah menjadi done dan Budi (Owner) menerima notifikasi.

### 5.3 Skenario: Keamanan (Security Attempt)
1. Seorang hacker mencoba mengirim request API paksa untuk mengubah status tugas tim padahal dia sudah dikeluarkan dari tim tersebut.
2. Firestore Rules: Mendeteksi auth.uid tidak ada di dalam array memberIds tim.
3. Hasil: Request ditolak dengan error PERMISSION_DENIED.

---

