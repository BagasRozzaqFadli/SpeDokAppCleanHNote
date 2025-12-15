# Timeline Sprint
## CleanHNote - Rencana Pengembangan 8 Minggu

---

### Informasi Tim
- **Nama Kelompok**: Bagas Rozzaq Fadli (221240001280) & Muhammad Alif Nur Hanif (221240001276)
- **Mata Kuliah**: Pemrograman Mobile Lanjut
- **Tanggal**: September 2025

---

## 1. Overview Timeline

- **Total Waktu**: 8 minggu
- **Jumlah Sprint**: 4 sprint
- **Durasi Sprint**: 2 minggu per sprint
- **Tim**: 2 developer

## 2. Sprint 1: Foundation & Authentication (Minggu 1-2)
Fokus: Menyiapkan infrastruktur Firebase dan manajemen user.

### Tujuan
- Koneksi project Flutter ke Firebase & Appwrite.
- Sistem Login/Register dengan pemisahan Role (free vs premium).
- Implementasi Mock Payment (Simulasi Upgrade).

### Checklist
- [✓] Setup: Inisialisasi Firebase Project (Auth, Firestore) & Appwrite Project.
- [✓] Auth: Implementasi Login Google & Email/Password.
- [✓] Database: Membuat struktur koleksi awal (users) di Firestore.
- [✓] Logic: Membuat fungsi upgrade premium (update field role & premiumExpiresAt).
- [✓] UI: Halaman Login, Register, dan Profil User.

## 3. Sprint 2: Personal Features & Core Logic (Minggu 3-4)
Fokus: Fitur dasar untuk pengguna gratis (Personal Task).

### Tujuan
- CRUD Tugas Pribadi (Create, Read, Update, Delete).
- State Management (Provider) terhubung ke Firestore Stream.
- Implementasi Notifikasi Lokal.

### Checklist
- [✓] Database: Setup koleksi personal_tasks dan indexing query.
- [✓] Logic: Membuat TaskProvider dengan snapshots() listener.
- [✓] UI: Halaman Home Personal dan Add Task Form.
- [✓] Feature: Implementasi Local Notification untuk pengingat jadwal.
- [✓] Testing: Cek fitur offline persistence (Buka aplikasi tanpa internet).

## 4. Sprint 3: Team Collaboration & Image Engine (Minggu 5-6)
Fokus: Fitur Premium, Logika Tim, dan Sistem Gambar Base64.

### Tujuan
- Manajemen Tim (Create, Join via Code, Member Logic).
- Security Rules: Menerapkan aturan validasi member di Firestore.
- Image Processing: Kompresi kamera ke Base64.
- Integrasi Appwrite untuk data analitik.

### Checklist
- [✓] Security: Deploy firestore.rules (Validasi memberIds & joinedTeamIds).
- [✓] Logic: Fitur Join Team (Update array user & team secara atomic/batch).
- [✓] Engine: Implementasi FlutterImageCompress -> Convert to Base64 String.
- [✓] Database: Setup koleksi team_assignments (dengan field foto embedded).
- [✓] Integration: Setup Trigger/Function: Saat tugas selesai -> Kirim data ke Appwrite (team_analytics).

## 5. Sprint 4: Analytics, Polishing & Finalization (Minggu 7-8)
Fokus: Visualisasi Data Appwrite, UI/UX, dan Laporan.

### Tujuan
- Menampilkan data dari Appwrite ke UI.
- Testing performa (Load time gambar Base64).
- Bug fixing dan penyusunan laporan.

### Checklist
- [✓] UI: Halaman Team Analytics (Grafik/Statistik dari Appwrite).
- [✓] UI/UX: Polish tampilan (Badge Premium, Loading State saat upload gambar).
- [✓] Testing: Stress test upload foto (memastikan size < 500KB).
- [✓] Docs: Finalisasi ERD, SDD, PRD, dan Manual Book.
- [ ] Release: Build APK Release version.

## 6. Pembagian Tugas Teknis

### Bagas Rozzaq Fadli (Frontend & UI/UX Specialist)
Tanggung Jawab:
- Desain antarmuka (Slicing UI) untuk seluruh halaman.
- Implementasi Widget Kamera & Preview Gambar (Base64 Decoder).
- Visualisasi Grafik Analitik (menggunakan library charts).
- State Management UI Binding (Menghubungkan Provider ke View).

### Muhammad Alif Nur Hanif (Backend & Logic Architect)
Tanggung Jawab:
- Konfigurasi Firebase Console & Appwrite Console.
- Penulisan Firestore Security Rules (Logic keamanan).
- Implementasi Service Logic: AuthService, FirestoreService, ImageService.
- Sinkronisasi data antara Firebase dan Appwrite.

## 7. Tech Stack & Tools (Updated)

- **Framework**: Flutter SDK (Dart).
- **Primary Backend**: Firebase (Auth, Firestore).
- **Secondary Backend**: Appwrite (Database for Analytics).

- Key Libraries:
  - cloud_firestore (Database).
  - flutter_image_compress (Image Engine).
  - provider (State Management).
  - flutter_local_notifications.
- **Design**: Figma.
- **Version Control**: Git & GitHub.
---
