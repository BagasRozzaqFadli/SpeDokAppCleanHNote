# Entity Relationship Diagram (ERD)
## CleanHNote - Database Design 
- **Note**: Tabel dibawah ini mungkin masih kurang sesuai dan mungkin masih disesuaikan lebih lanjut

---

### Informasi Tim
- **Nama Kelompok**: Bagas Rozzaq Fadli (221240001280) & Muhammad Alif Nur Hanif (221240001276)
- **Mata Kuliah**: Pemrograman Mobile Lanjut
- **Tanggal**: September 2025

---

## 1. Database Overview

Sistem ini menggunakan struktur **NoSQL (Collection & Document)**.
- **Primary DB (Firestore)**: Menyimpan data User, Tugas, Tim, dan Thumbnail Foto (Base64).
- **Secondary DB (Appwrite)**: Menyimpan Arsip Analytics dan Backup Bukti Foto Resolusi Tinggi.

## 2. Struktur Data (Collections)

### 2.1 Tabel Pengguna
| Field | Type | Keterangan |
|-------|------|------------|
| uid (PK) | String | ID unik dari Firebase Auth |
| email | String | Email pengguna |
| name | String | Nama pengguna |
| Role | String | free, premium |
| created_at | Date | Tanggal daftar |
| premiumExpDate | Date | Tanggal Habis Langganan bila memiliki role premium |
| penggunaId | String | ID acak dari sistem |
| gabungTimId | Array<String> | List ID Tim yang diikuti (Max: 3 Free / 15 Premium) |

### 2.2 Tabel Tasks (Tugas)
| Field | Type | Keterangan |
|-------|------|------------|
| id | String | ID unik tugas |
| title | String | Judul tugas |
| description | String | Deskripsi tugas |
| user_id | String | ID pembuat tugas dari tabel users |
| category | String | General, Maintenance, Cleaning, Organization |
| status | String | pending, completed, cancelled |
| level | String | Easy, Hard |
| priority | String | low, medium, high |
| time | Timestamp | Waktu tugas|
| due_date | Date | Tanggal deadline |
| created_at | Date | Tanggal dibuat |

### 2.3 Tabel Teams (Tim)
| Field | Type | Keterangan |
|-------|------|------------|
| id | String | ID unik tim |
| name | String | Nama tim |
| owner_id | String | ID pemilik tim diambil dari user ID yang membuat tim |
| invitation_code | String | kode unik untuk gabung tim |
| memberIds | Array<String> | ID anggota tim |
| created_at | Date | Tanggal dibuat |

### 2.4 Tabel Tasks_Team (Tugas Tim)
| Field | Type | Keterangan |
|-------|------|------------|
| id | String | ID unik tugas |
| title | String | Judul tugas |
| description | String | Deskripsi tugas |
| user_id | String | ID pembuat tugas |
| assigned_to | String | ID yang ditugaskan |
| teams_id | String | ID tim |
| status | String | pending, completed, late |
| level | String | Easy, Hard |
| priority | String | low, medium, high |
| time | Timestamp | Waktu tugas|
| due_date | Date | Tanggal deadline |
| viewedByMember | Boolean | sudah, belumnya dilihat anggota |
| viewedByMember | Boolean | sudah, belumnya dilihat pemilik |
| created_at | Date | Tanggal dibuat |

### 2.5 Tabel Photos (Foto) ==> tolong baharui
| Field | Type | Keterangan |
|-------|------|------------|
| id | String | ID unik foto |
| task_id | String | ID tugas terkait |
| photo_type | String | before atau after |
| file_url | String | URL file foto |
| uploaded_at | Date | Tanggal upload |

### 2.6 Tabel Notification (notifikasi)
| Field | Type | Keterangan |
|-------|------|------------|
| notifId (PK) | String | ID Notifikasi |
| userId | String (FK) | Penerima notifikasi |
| taskId | String (FK) | ID Tugas terkait |
| taskTitle | String | Judul tugas |
| timId | String (FK) | Bila tugas dari tim |
| shown | Boolean | sudah, belumnya notifikasi dilihat |
| notificationType | String | tipe notifikasi tugas |

### 2.7 Tabel Analisis_Tim
| Field | Type | Keterangan |
|-------|------|------------|
| $id (PK) |	String |	Appwrite Document ID |
| teamId |	String |	Referensi ke Firebase teams.teamId |
| teamName |	String |	Nama tim (Snapshot) |
| ownerId	| String |	Referensi ke Firebase users.uid |
| members |	String (Long Text) |	JSON String berisi list nama member (Snapshot) |
| monthlyTrends |	String (Long Text) |	JSON String berisi data koordinat grafik bulanan |
| totalTasksCreated |	Integer |	Counter total tugas dibuat |
| totalTasksCompleted |	Integer	| Counter total tugas selesai |
| totalTasksLate |	Integer |	Counter total tugas telat |
| totalTasksPending |	Integer |	Counter total tugas tertunda |
| averageCompletionTime	| Double |	Rata-rata waktu penyelesaian (jam/menit) |
| createdAt	| DateTime |	Waktu record analitik dibuat |
| lastUpdated |	DateTime |	Waktu sinkronisasi terakhir |

### 2.8 Tabel Payment (Memastikan pembayaran)

## 3. Hubungan Antar Tabel

```
Users (1) ──── (∞) Personal_Tasks
  │
  ├── (1) ──── (∞) Teams (sebagai Owner)
  │
  └── (∞) ──── (∞) Teams (sebagai Member/Joined)

Teams (1) ──── (∞) Team_Assignments
   │                 │
   │                 └── (Embedded) Photos (Base64 String)
   │
   └── (1) ──── (1) Team_Analytics (Database Appwrite)
```

## 4. Penjelasan Hubungan

1. Users ── (∞) Teams (Member): Hubungan ini sekarang bersifat Many-to-Many (Satu user bisa masuk banyak tim, satu tim punya banyak user). Diimplementasikan via array ID, tidak lagi butuh tabel perantara Team_Members.
2. Team_Assignments ── (Embedded) Photos: Tidak ada lagi hubungan (1) ──── (∞) ke tabel Photos terpisah. Foto kini "menempel" langsung di dalam dokumen tugas (photoBeforeBase64, photoAfterBase64) agar lebih cepat dimuat.
3. Teams ── (1) Team_Analytics: Hubungan baru antar-server. Satu Tim di Firebase memiliki satu dokumen statistik khusus di Appwrite.
---

