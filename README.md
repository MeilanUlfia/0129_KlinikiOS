# Klinik iOS

**Aplikasi Pengaduan Kerusakan dan Estimasi Biaya Servis iPhone dan iPad**

| Informasi | Keterangan |
|---|---|
| Mata kuliah | Pengembangan Aplikasi Mobile (Project Akhir 2026) |
| Pembuat | Meilan Ulfia Nurfari'ah (20240140129) |
| Platform | Android (Kotlin, Jetpack Compose) |
| Tema | UMKM |
| Status | Tahap perancangan, implementasi belum dimulai |

---

## 1. Ringkasan

Klinik iOS membantu konter servis perangkat Apple mengelola pengaduan kerusakan. Pelanggan mengirim pengaduan berisi keluhan dan foto kerusakan (opsional). Admin, yaitu pemilik konter, menganalisis apakah kerusakan bisa ditangani berdasarkan data Perangkat, Layanan, dan Sparepart. Jika bisa, sistem menerbitkan invoice estimasi biaya (biaya jasa + harga sparepart).

## 2. Tujuan

1. Menggantikan pencatatan pengaduan manual lewat chat.
2. Memberi pelanggan kepastian status dan estimasi biaya sebelum perbaikan.
3. Membantu Admin menilai kemampuan konter secara konsisten berdasarkan data.

## 3. Ruang Lingkup

**Termasuk**
- Daftar, login, refresh token, dan logout
- Dua role: Admin dan Pelanggan
- Kelola data Perangkat, Layanan, dan Sparepart (Admin)
- Pengaduan dengan foto kerusakan opsional (Pelanggan)
- Analisis pengaduan dan invoice estimasi biaya
- Perangkat iPhone dan iPad

**Tidak termasuk**
- Pembayaran online
- Pelacakan progres perbaikan setelah invoice terbit
- Notifikasi push dan chat
- Perangkat selain iPhone dan iPad

## 4. Pengguna

| Role | Cara akun dibuat | Kegunaan |
|---|---|---|
| Admin | Seed data di database (tanpa daftar) | Kelola data master, analisis pengaduan, kelola invoice |
| Pelanggan | Daftar lewat aplikasi (role otomatis `pelanggan`) | Buat pengaduan, pantau status, lihat invoice miliknya |

Login memakai satu halaman. Setelah masuk, aplikasi membaca role dari token lalu menampilkan menu sesuai role.

## 5. Alur Singkat

1. Pelanggan login, lalu membuat pengaduan: nama, no kontak, model perangkat, keluhan, dan foto (opsional). Status awal **Diadukan**.
2. Admin membuka pengaduan dan menjalankan analisis. Sistem mengecek apakah kombinasi Perangkat + Layanan memiliki Sparepart.
3. Jika ada, status menjadi **Bisa Ditangani** dan invoice terisi otomatis. Jika tidak, status menjadi **Tidak Bisa Ditangani** disertai catatan Admin.
4. Pelanggan melihat status dan invoice miliknya.

## 6. Teknologi (rencana)

| Bagian | Teknologi |
|---|---|
| UI | Jetpack Compose, Material 3 |
| Navigasi | Navigation Compose |
| State management | MVVM (ViewModel + Repository) |
| REST client dan JSON | Retrofit + Gson |
| Backend | Node.js + Express |
| Database | MySQL |
| Autentikasi | JWT (access token + refresh token), password di-hash |
| File storage | Foto kerusakan diunggah ke backend (agar bisa dilihat Admin), salinannya disimpan dan dibaca dari penyimpanan lokal aplikasi |

## 7. Status Fitur terhadap Pedoman PAM 2026

Keterangan: **Belum** = belum dikerjakan, **Proses** = sedang dikerjakan, **Selesai** = sudah berjalan.

| Ketentuan Pedoman | Rencana implementasi | Status |
|---|---|---|
| Login, register, JWT, refresh token | Layar Login/Daftar, endpoint auth, interceptor token | Belum |
| Otorisasi berbasis role | Role `admin` dan `pelanggan`, menu berbeda, backend menolak akses salah role | Belum |
| Navigasi multi-halaman | Navigation Compose | Belum |
| State management MVVM | ViewModel + Repository per fitur | Belum |
| Minimal 4 entitas saling terhubung | Users, Perangkat, Layanan, Sparepart, Pengaduan, Invoice | Belum |
| Minimal 5 fitur CRUDS penuh | Perangkat, Layanan, Sparepart, Pengaduan, Invoice | Belum |
| REST API dan parsing JSON | Retrofit + Gson | Belum |
| Database MySQL | MySQL, tanpa Room | Belum |
| File storage lokal | Foto kerusakan disimpan dan dibaca dari penyimpanan lokal aplikasi, sekaligus diunggah ke backend | Belum |
| UI hanya Jetpack Compose | Seluruh layar Compose | Belum |
| README berisi status fitur dan progress mingguan | Dokumen ini | Proses |

## 8. Matriks CRUDS (rencana)

| Fitur | Create | Read | Update | Delete | Search |
|---|---|---|---|---|---|
| Perangkat | `POST /perangkat` | `GET /perangkat` | `PUT /perangkat/{id}` | `DELETE /perangkat/{id}` | `?q=` |
| Layanan | `POST /layanan` | `GET /layanan` | `PUT /layanan/{id}` | `DELETE /layanan/{id}` | `?q=` |
| Sparepart | `POST /sparepart` | `GET /sparepart` | `PUT /sparepart/{id}` | `DELETE /sparepart/{id}` | `?q=` |
| Pengaduan | `POST /pengaduan` | `GET /pengaduan` | `PUT /pengaduan/{id}` | `DELETE /pengaduan/{id}` | `?q=`, `?status=` |
| Invoice | `POST /pengaduan/{id}/analisis` | `GET /invoice` | `PUT /invoice/{id}` | `DELETE /invoice/{id}` | `?q=` |

Hak akses: data master dan invoice dikelola Admin. Pelanggan hanya dapat membaca data Perangkat (untuk memilih model saat mengadu), mengubah, menghapus, dan mencari pengaduannya sendiri, serta melihat invoice miliknya.

## 9. Entitas Basis Data

| Tabel | Isi utama | Relasi |
|---|---|---|
| `users` | nama, email, password hash, no HP, role | 1 user punya banyak `pengaduan` |
| `perangkat` | kategori (iPhone/iPad), nama model | 1 perangkat punya banyak `sparepart` dan `pengaduan` |
| `layanan` | nama perbaikan, biaya jasa | 1 layanan punya banyak `sparepart` |
| `sparepart` | nama, harga | milik 1 `perangkat` dan 1 `layanan` |
| `pengaduan` | keluhan, foto, status, catatan admin | milik 1 `users`, merujuk 1 `perangkat` |
| `invoice` | biaya jasa, harga sparepart, total | 1 `pengaduan` memiliki 1 `invoice`, merujuk `layanan` dan `sparepart` |
| `refresh_tokens` | token, masa berlaku | milik 1 `users` |

Aturan "bisa atau tidak": pengaduan **bisa ditangani** jika kombinasi Perangkat + Layanan memiliki Sparepart. Harga pada invoice disalin saat invoice terbit, sehingga perubahan harga di kemudian hari tidak mengubah invoice lama.

## 10. Cara Menjalankan

Akan diisi setelah implementasi: kebutuhan (Android Studio, Node.js, MySQL), konfigurasi `.env`, import skema dan seed, menjalankan backend, dan menjalankan aplikasi. Akun Admin demo dicantumkan di bagian ini.

## 11. Progress Mingguan

Kewajiban pedoman: minimal satu progress per minggu, disertai commit.

### 11.1 Progress yang sudah dikerjakan

| Minggu | Tanggal | Progress | Commit |
|---|---|---|---|
| 1 | 11 Nov 2026 | Penentuan tema, judul, dan konsep aplikasi; disetujui asisten | - |
| 1 | 11-12 Nov 2026 | Perancangan entitas, relasi, role, dan alur pengguna | - |
| 2 | 22 Nov 2026 | Penyusunan SRS Bab 1 dan 2 | - |
| 3 | 23 Nov 2026 | Pembuatan repository dan README awal | (isi hash commit ini) |

### 11.2 Rencana commit berikutnya

Target total minimal 30 commit, dikerjakan bertahap.

| # | Kelompok pekerjaan | Perkiraan commit | Status |
|---|---|---|---|
| 1 | Setup project Android, Gradle, `.gitignore` | 2 | Belum |
| 2 | Tema: warna, tipografi, `strings.xml`, `dimens.xml` | 2 | Belum |
| 3 | Model data, klien REST API, penyimpanan sesi token | 3 | Belum |
| 4 | Navigasi Compose | 2 | Belum |
| 5 | Backend: Express, skema MySQL, seed Admin | 2 | Belum |
| 6 | Backend: autentikasi JWT dan refresh token | 2 | Belum |
| 7 | Login dan Daftar (ViewModel + layar) | 3 | Belum |
| 8 | Beranda dan menu per role | 2 | Belum |
| 9 | Backend dan layar CRUDS Perangkat | 2 | Belum |
| 10 | Backend dan layar CRUDS Layanan | 2 | Belum |
| 11 | Backend dan layar CRUDS Sparepart | 2 | Belum |
| 12 | Backend dan layar CRUDS Pengaduan | 3 | Belum |
| 13 | File storage: foto kerusakan | 1 | Belum |
| 14 | Analisis pengaduan dan invoice (Admin) | 3 | Belum |
| 15 | Layar invoice Pelanggan | 1 | Belum |
| 16 | Validasi input dan penyempurnaan UI | 2 | Belum |
| 17 | Pengujian dan perbaikan bug | 2 | Belum |
| 18 | README final: status fitur, cara menjalankan | 1 | Belum |
