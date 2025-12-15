# Software Design Document (SDD)
## CleanHNote - Desain Arsitektur Aplikasi (Hybrid Version)

---

### Informasi Tim
- **Nama Kelompok**: Bagas Rozzaq Fadli (221240001280) & Muhammad Alif Nur Hanif (221240001276)
- **Mata Kuliah**: Pemrograman Mobile Lanjut
- **Tanggal**: September 2025

---

## 1. Arsitektur Aplikasi (Hybrid System)

CleanHNote menerapkan arsitektur Hybrid Backend untuk memaksimalkan performa real-time sekaligus kemampuan analisis data.
- **Primary Backend (Firebase)**: Menangani Autentikasi, Database Operasional, dan Logika Keamanan.
- **Secondary Backend (Appwrite)**: Menangani penyimpanan arsip analitik tim dan pencatatan tren kinerja.

Diagram Arsitektur (Logical Flow)

```
[Flutter Client App]
      │
      │ ◄── (Auth & Realtime Data) ──► [Firebase Services]
      │                                     ├── Authentication
      │                                     ├── Firestore DB (Main)
      │                                     └── Security Rules
      │
      └── (Send Analytics Data) ─────▶ [Appwrite Services]
                                            └── Team Analytics DB
```

Diagram Relasi Entitas (Logical Data Structure)

```
Users
│
├── (1 : ∞) ──> Personal_Tasks
│
├── (1 : ∞) ──> Teams (sebagai Owner)
│
└── (∞ : ∞) ──> Teams (sebagai Member/Joined)
      │
      ├── (1 : 1) ──> Team_Analytics (Sync ke Appwrite)
      │
      └── (1 : ∞) ──> Team_Assignments
             │
             └── (Embedded) ──> Photos (Base64 String)
```

## 2. Struktur Folder Flutter

```
lib/
├── config/               # Konfigurasi Environment & Keys
│   ├── firebase_options.dart
│   └── appwrite_config.dart
├── screens/              # Halaman Antarmuka
│   ├── auth/             # Login, Register
│   ├── personal/         # Home Personal, Add Task
│   ├── team/             # Team Dashboard, Assignment Detail
│   └── analytics/        # (Premium) Grafik performa dari Appwrite
├── widgets/              # Komponen UI Reusable
│   ├── task_card.dart
│   ├── premium_badge.dart
│   └── evidence_camera.dart  # Widget khusus foto & kompresi
├── models/               # Data Model (JSON Serialization)
│   ├── app_user.dart
│   ├── team_assignment.dart  # Berisi field base64 image
│   └── team_analytics.dart   # Model parsing data Appwrite
├── services/             # Logic Layer
│   ├── auth_service.dart     # Firebase Auth Wrapper
│   ├── firestore_service.dart # CRUD Data Utama
│   ├── appwrite_service.dart  # Sync & Fetch Analytics
│   └── image_service.dart     # Compress & Base64 Encoder
└── utils/
    └── date_helper.dart
```

## 3. Komponen Utama

### 3.1 Screens (Halaman)
- **LoginScreen**: Halaman login dan daftar
- **HomeScreen**: Halaman utama dengan daftar tugas
- **TaskScreen**: Halaman detail dan buat tugas
- **TeamScreen**: Halaman kelola tim (Premium)

### 3.2 Widgets (Komponen UI)
- **TaskCard**: Kartu untuk menampilkan tugas
- **CustomButton**: Tombol dengan desain konsisten
- **PhotoGallery**: Galeri foto dokumentasi

### 3.3 Models (Data)
- **User**: Data pengguna (nama, email, subscription)
- **Task**: Data tugas (judul, deskripsi, status, dll)
- **Team**: Data tim (nama, owner, anggota)

### 3.4 Services (Layanan)
- **AuthService**: Login, daftar, logout
- **TaskService**: CRUD operasi tugas
- **TeamService**: Kelola tim dan anggota

## 4. Database Design

### 4.1 Collections di Appwrite
- **users**: Data pengguna
- **tasks**: Data tugas
- **teams**: Data tim
- **photos**: Data foto

### 4.2 Permissions
- User hanya bisa akses data miliknya
- Team owner bisa akses data tim
- Team member bisa akses data tim

## 5. API Integration

### 5.1 Authentication
```dart
final FirebaseAuth _auth = FirebaseAuth.instance;
final GoogleSignIn _googleSignIn = GoogleSignIn();

// 1. Login dengan Email & Password
Future<void> login(String email, String password) async {
  await _auth.signInWithEmailAndPassword(
    email: email,
    password: password,
  );
}

// 2. Daftar (Register) dengan Email & Password
Future<void> register(String email, String password, String name) async {
  // Buat akun baru
  UserCredential userCredential = await _auth.createUserWithEmailAndPassword(
    email: email,
    password: password,
  );
  
  // Update Display Name (Nama Pengguna) segera setelah daftar
  await userCredential.user?.updateDisplayName(name);
}

// 3. Login dengan Google (Google Sign-In)
Future<User?> signInWithGoogle() async {
  // Trigger flow autentikasi Google
  final GoogleSignInAccount? googleUser = await _googleSignIn.signIn();
  
  if (googleUser != null) {
    // Dapatkan detail otentikasi dari request
    final GoogleSignInAuthentication googleAuth = await googleUser.authentication;

    // Buat kredensial baru
    final OAuthCredential credential = GoogleAuthProvider.credential(
      accessToken: googleAuth.accessToken,
      idToken: googleAuth.idToken,
    );

    // Sign in ke Firebase dengan kredensial Google
    final UserCredential userCredential = await _auth.signInWithCredential(credential);
    return userCredential.user;
  }
  return null;
}

// 4. Logout
Future<void> logout() async {
  await _googleSignIn.signOut(); // Logout dari Google (jika ada)
  await _auth.signOut();         // Logout dari Firebase
}
```

### 5.2 Database Operations
```dart
final FirebaseFirestore _firestore = FirebaseFirestore.instance;

//Buat Tugas Baru (Create Task)
Future<void> createTask({
  required String title,
  required String description,
  required String userId,
}) async {
  try {
    // Menambahkan dokumen baru dengan ID otomatis (Auto-ID)
    await _firestore.collection('personal_tasks').add({
      'title': title,
      'description': description,
      'userId': userId, // Sesuai field di ERD baru
      'status': 'pending',
      'createdAt': FieldValue.serverTimestamp(), // Menggunakan waktu server
    });
  } catch (e) {
    print('Error creating task: $e');
    rethrow;
  }
}
```

## 6. State Management

Menggunakan **Provider** untuk mengelola state:

```dart
import 'package:flutter/material.dart';
import 'package:cloud_firestore/cloud_firestore.dart';
import '../models/task.dart'; // Pastikan model Task sudah dibuat

class TaskProvider extends ChangeNotifier {
  final FirebaseFirestore _firestore = FirebaseFirestore.instance;
  
  // List lokal yang akan dikonsumsi oleh UI
  List<Task> _tasks = [];
  List<Task> get tasks => _tasks;

  // Constructor: Langsung pasang pendengar (listener) saat Provider dibuat
  TaskProvider() {
    _initRealtimeListener();
  }

  // 1. Mendengarkan Perubahan Data (Real-time Sync)
  void _initRealtimeListener() {
    // Mengambil data dari collection 'personal_tasks'
    _firestore.collection('personal_tasks')
      .orderBy('date_time', descending: false) // Urutkan berdasarkan waktu
      .snapshots() // Stream: Akan trigger setiap ada perubahan di DB
      .listen((snapshot) {
        
        // Konversi dokumen Firestore menjadi List Object Task
        _tasks = snapshot.docs.map((doc) {
          return Task.fromFirestore(doc); 
        }).toList();

        // Beritahu UI untuk update tampilan
        notifyListeners();
      });
  }

  // 2. Menambah Data (Write to Firestore)
  // Note: Kita tidak perlu _tasks.add() manual, karena Stream di atas akan otomatis
  // mendeteksi data baru dan memperbarui list local.
  Future<void> addTask(Task task) async {
    try {
      await _firestore.collection('personal_tasks').add(task.toMap());
    } catch (e) {
      print("Error adding task: $e");
      rethrow;
    }
  }

  // 3. Mengupdate Data
  Future<void> updateTask(String taskId, Map<String, dynamic> data) async {
    try {
      await _firestore.collection('personal_tasks').doc(taskId).update(data);
    } catch (e) {
      print("Error updating task: $e");
      rethrow;
    }
  }
  
  // 4. Menghapus Data
  Future<void> deleteTask(String taskId) async {
      await _firestore.collection('personal_tasks').doc(taskId).delete();
  }
}
```

## 7. UI/UX Design

### 7.1 Design System
- **Warna**: Primary (Biru), Secondary (Hijau), Error (Merah)
- **Font**: Roboto untuk Android
- **Icon**: Material Icons
- **Spacing**: 8px, 16px, 24px, 32px

### 7.2 Screen Layouts
- **Login**: Form di tengah layar
- **Home**: List tugas dengan FAB untuk tambah
- **Task Detail**: Form edit dengan tombol save
- **Team**: List anggota dengan tombol invite

## 8. Security

### 8.1 Data Protection
- Password dienkripsi oleh Appwrite
- Token untuk autentikasi tim

### 8.2 Access Control
- User hanya bisa akses data miliknya
- Premium features dicek di frontend dan backend

## 9. Performance

### 9.1 Optimization
- Lazy loading untuk list tugas
- Image compression untuk foto
- Caching data di local storage

### 9.2 Error Handling
- Try-catch untuk semua API calls
- User-friendly error messages
- Retry mechanism untuk network errors

---
