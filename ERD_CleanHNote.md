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

CleanHNote menggunakan Appwrite Database dengan 4 tabel utama:

## 2. Tabel Database

### 2.1 Tabel Users (Pengguna)
| Field | Type | Keterangan |
|-------|------|------------|
| id | String | ID unik pengguna |
| email | String | Email pengguna |
| name | String | Nama pengguna |
| Role | String | free, premium, admin |
| created_at | Date | Tanggal daftar |
| premiumExpDate | Date | Tanggal Habis Langganan bila memiliki role premium |

### 2.2 Tabel Tasks (Tugas)
| Field | Type | Keterangan |
|-------|------|------------|
| id | String | ID unik tugas |
| title | String | Judul tugas |
| description | String | Deskripsi tugas |
| user_id | String | ID pembuat tugas dari tabel users |
| status | String | pending, completed, cancelled |
| priority | String | low, medium, high, urgent |
| due_date | Date | Tanggal deadline |
| created_at | Date | Tanggal dibuat |

### 2.3 Tabel Teams (Tim)
| Field | Type | Keterangan |
|-------|------|------------|
| id | String | ID unik tim |
| name | String | Nama tim |
| owner_id | String | ID pemilik tim diambil dari user ID yang membuat tim |
| invitation_code | String | kode unik untuk gabung tim |
| description | String | Deskripsi tim |
| created_at | Date | Tanggal dibuat |

### 2.4 Tabel Team_Memmbers (Tim)
| Field | Type | Keterangan |
|-------|------|------------|
| id | String | ID unik tim |
| user_id | String | ID anggota |
| teams_id | String | ID tim |
| role | String | Owner, memmber |
| joined_at | Date | Tanggal masuk team |
| created_at | Date | Tanggal dibuat |

### 2.5 Tabel Tasks_Team (Tugas Tim)
| Field | Type | Keterangan |
|-------|------|------------|
| id | String | ID unik tugas |
| title | String | Judul tugas |
| description | String | Deskripsi tugas |
| user_id | String | ID pembuat tugas |
| assigned_to | String | ID yang ditugaskan |
| teams_id | String | ID tim (jika ada) |
| status | String | pending, completed, cancelled |
| priority | String | low, medium, high, urgent |
| due_date | Date | Tanggal deadline |
| created_at | Date | Tanggal dibuat |

### 2.6 Tabel Photos (Foto) ==> tolong baharui
| Field | Type | Keterangan |
|-------|------|------------|
| id | String | ID unik foto |
| task_id | String | ID tugas terkait |
| photo_type | String | before atau after |
| file_url | String | URL file foto |
| uploaded_at | Date | Tanggal upload |

### 2.7 Tabel Teams_Chat (Chatting dengan teman satu tim)

### 2.8 Tabel Notification (notifikasi)

### 2.9 Tabel Payment (Memastikan pembayaran)

## 3. Hubungan Antar Tabel

```
Users (1) ──── (∞) Tasks
  │
  └── (1) ──── (∞) Teams (sebagai owner)

Teams (1) ──── (∞) Tasks (tugas tim)

Tasks (1) ──── (∞) Photos (foto dokumentasi)
```

## 4. Penjelasan Hubungan

- **1 User** bisa punya **banyak Tasks**
- **1 User** bisa punya **banyak Teams** (sebagai owner)
- **1 Team** bisa punya **banyak Tasks**
- **1 Task** bisa punya **banyak Photos**



---

