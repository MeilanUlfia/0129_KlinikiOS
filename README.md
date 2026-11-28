# Klinik iOS

**Aplikasi Pengaduan Kerusakan dan Estimasi Biaya Servis iPhone dan iPad**

| Informasi | Keterangan |
|---|---|
| Mata kuliah | Pengembangan Aplikasi Mobile (Project Akhir 2026) |
| Pembuat | Meilan Ulfia Nurfari'ah (20240140129) |
| Platform | Android 7.0 (API 24) ke atas, Kotlin, Jetpack Compose |
| Tema | UMKM |
| Status | Penyusunan SRS, implementasi belum dimulai |

---

## 1. Ringkasan

Pelanggan mengirim pengaduan kerusakan iPhone/iPad berisi model perangkat, keluhan, dan foto (opsional). Admin (pemilik konter) menganalisis pengaduan berdasarkan data Perangkat, Layanan, dan Sparepart. Jika sparepart tersedia, Admin menerbitkan invoice estimasi biaya (biaya jasa + harga sparepart) yang dapat dilihat dan disimpan Pelanggan.

## 2. Ruang Lingkup

**Termasuk:** registrasi, login, refresh token, logout · dua peran (Admin dan Pelanggan) · kelola Perangkat, Layanan, Sparepart · pengaduan dengan foto dari galeri · analisis dengan empat status · invoice estimasi · simpan invoice ke perangkat dan dibuka tanpa internet.

**Tidak termasuk:** pembayaran online · booking/antrean · pelacakan perbaikan · chat dan notifikasi push · perangkat selain iPhone/iPad · invoice servis tanpa sparepart.

## 3. Pengguna

| Peran | Akun | Kegunaan |
|---|---|---|
| Admin | Seed di database | Kelola data master, analisis pengaduan, kelola invoice |
| Pelanggan | Registrasi (nama, email, no HP, kata sandi) | Kelola pengaduan miliknya, lihat dan simpan invoice |

Login memakai email dan kata sandi. Menu ditampilkan sesuai peran.

## 4. Alur dan Status Pengaduan

| Status | Kondisi | Invoice |
|---|---|---|
| Diadukan | Pengaduan baru; Pelanggan masih dapat mengubah atau menghapus | — |
| Bisa Ditangani | Layanan dipilih dan sparepart tersedia | Terbit |
| Tidak Bisa Ditangani | Layanan dipilih, sparepart tidak tersedia | — |
| Perlu Cek Langsung | Kerusakan tidak dapat dipastikan atau hanya butuh jasa; perangkat dibawa ke konter | — |

Admin dapat mengubah biaya invoice dengan alasan, atau menghapus invoice sehingga status kembali Diadukan.

## 5. Teknologi (rencana)

| Bagian | Teknologi |
|---|---|
| Android | Jetpack Compose (Material 3), Navigation Compose, MVVM |
| REST client | Retrofit + kotlinx.serialization + OkHttp |
| Token dan foto | DataStore, Coil |
| Backend | Node.js + Express (mysql2, jsonwebtoken, bcrypt, multer) |
| Database | MySQL 8 (Laragon) |
| Autentikasi | JWT: access token 15 menit, refresh token 7 hari |
| File lokal | Invoice Tersimpan di penyimpanan internal aplikasi |

## 6. Status Fitur terhadap Pedoman PAM 2026

| Ketentuan | Rencana | Status |
|---|---|---|
| Login, register, JWT, refresh token | Splash, Login, Registrasi, endpoint auth | Belum |
| Otorisasi berbasis peran | Menu per peran, backend menolak akses tanpa hak | Belum |
| Navigasi multi-halaman | Navigation Compose | Belum |
| State management MVVM | ViewModel + Repository | Belum |
| Minimal 4 entitas terhubung | 6 entitas (lihat bagian 8) | Belum |
| Minimal 5 fitur CRUDS | Perangkat, Layanan, Sparepart, Pengaduan, Invoice | Belum |
| REST API dan JSON | Retrofit + kotlinx.serialization | Belum |
| Database | MySQL | Belum |
| File storage lokal | Invoice Tersimpan | Belum |
| UI Jetpack Compose | Seluruh halaman | Belum |
| SRS | Bab 1–6 dan Appendix A | Proses |
| README | Dokumen ini | Proses |

## 7. Matriks CRUDS

| Fitur | Admin | Pelanggan |
|---|---|---|
| Perangkat | C R U D S | R (memilih model) |
| Layanan | C R U D S | — |
| Sparepart | C R U D S | — |
| Pengaduan | R S | C R U D (miliknya, ubah/hapus saat Diadukan) |
| Invoice | C R U D S | R (miliknya) |

## 8. Entitas Basis Data

| Tabel | Isi utama | Relasi |
|---|---|---|
| `pengguna` | nama, email, no HP, kata sandi, peran | 1 : N `pengaduan` |
| `perangkat` | kategori, nama model | 1 : N `sparepart`, `pengaduan` |
| `layanan` | nama layanan, biaya jasa | 1 : N `sparepart`, `pengaduan` |
| `sparepart` | nama, harga | milik 1 `perangkat` + 1 `layanan` |
| `pengaduan` | keluhan, foto, status, catatan Admin | milik 1 `pengguna` |
| `invoice` | nomor, salinan nama dan harga, total | 1 : 1 `pengaduan` |

Invoice menyimpan salinan harga, sehingga perubahan data master tidak mengubah invoice lama.

## 9. Cara Menjalankan

Diisi setelah implementasi (kebutuhan, `.env`, skema dan seed, menjalankan backend dan aplikasi, akun Admin demo).

## 10. Progress Mingguan (Weekly Progress)

Linimasa pengerjaan berdasarkan riwayat pengerjaan dan commit. Tahap saat ini: penyusunan SRS.

### 🗓️ Minggu 1 (9 – 15 November 2026)

- **Tema dan Judul**: Tema UMKM, judul Klinik iOS, disetujui asisten (11 Nov).
- **Konsep Awal**: Entitas, relasi, peran, dan alur aplikasi (11–12 Nov).

### 🗓️ Minggu 2 (16 – 22 November 2026)

- **SRS Bab 1 dan 2** (v0.1): Introduction dan Overall Description.

### 🗓️ Minggu 3 (23 – 28 November 2026)

- **Repository dan README** (`57c5307`, `37a981c`): Pembuatan repository dan README awal.
- **Finalisasi Rancangan**: 6 entitas beserta tipe data, 4 status pengaduan, aturan invoice, endpoint REST API, dan teknologi.
- **SRS Bab 3–6** (v0.2): 8 fitur dengan 40 REQ, antarmuka, kebutuhan nonfungsional, relasi tabel, dan kamus data.
- **Revisi SRS** (v1.0): Peringkasan, revisi Bab 4–5, kamus data, dan glosarium. *Diagram belum disisipkan.*
- **Sinkronisasi README** (`hash commit ini`): README disesuaikan dengan SRS.

### 🗓️ Rencana Minggu 4

- Menyisipkan diagram SRS: use case, flowchart, activity diagram, RAT, dan ERD.
- Setup project Android dan backend.
