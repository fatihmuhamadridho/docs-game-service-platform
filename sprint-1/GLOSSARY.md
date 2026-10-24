# Glosarium Sprint 1

Glosarium ini menjelaskan istilah yang digunakan dalam BRD, HLD, ERD, dan solutioning Sprint 1. Istilah khusus endpoint atau fitur dijelaskan lebih rinci pada dokumen solutioning terkait.

| Istilah                                 | Definisi                                                                                                                                                                         |
| --------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Admin account (akun administrator)      | Identitas administrator yang digunakan untuk masuk ke dashboard dan diberi satu role.                                                                                            |
| API (Application Programming Interface) | Antarmuka yang digunakan aplikasi untuk bertukar request dan response dengan service lain.                                                                                       |
| Autentikasi (authentication)            | Proses memverifikasi identitas pengguna atau service yang mengirim request.                                                                                                      |
| Authorization (otorisasi)               | Pemeriksaan apakah identitas yang telah terautentikasi boleh menjalankan tindakan atau mengakses data tertentu.                                                                  |
| Bootstrap                               | Proses operasional untuk menyiapkan data awal sistem. Di Sprint 1, proses ini membuat role `SUPER_ADMIN` dan akun Super Admin melalui backend, database, atau script terkendali. |
| BRD (Business Requirements Document)    | Dokumen yang menjelaskan konteks, tujuan, ruang lingkup, kebutuhan bisnis, asumsi, dependensi, dan pertanyaan terbuka.                                                           |
| ERD (Entity-Relationship Diagram)       | Diagram yang menggambarkan entitas data, atribut, dan hubungan antarentitas.                                                                                                     |
| Game catalog (katalog game)             | Kumpulan data game yang dikelola administrator dan menjadi sumber untuk daftar game publik. Kategori dan layanan tidak termasuk katalog Sprint 1.                                |
| HLD (High-Level Design)                 | Gambaran tingkat tinggi tentang teknologi, arsitektur, dan alur utama sistem.                                                                                                    |
| IFA (Interface/Functional Analysis)     | Analisis fitur atau API yang menjelaskan alur, kontrak input-output, validasi, aturan, dan error.                                                                                |
| NFT (Non-Functional Test)               | Pengujian karakteristik nonfungsional, seperti beban, kapasitas, dan waktu respons.                                                                                              |
| Permission (izin)                       | Hak untuk menjalankan tindakan tertentu pada fitur atau data.                                                                                                                    |
| RBAC (Role-Based Access Control)        | Model kontrol akses yang menghubungkan izin dengan role, lalu memberikan role kepada akun administrator.                                                                         |
| Role                                    | Kumpulan izin yang menentukan tindakan yang dapat dilakukan akun administrator. Satu akun administrator Sprint 1 memiliki satu role.                                             |
| Session (sesi)                          | Keadaan autentikasi yang mempertahankan akses pengguna setelah identitasnya diverifikasi, selama sesi masih berlaku.                                                             |
| SLA (Service Level Agreement)           | Target tingkat layanan. Target Sprint 1 masih berupa baseline awal dan perlu divalidasi melalui pengujian.                                                                       |
| `SUPER_ADMIN` (Super Admin)             | Role system awal dengan akses ke seluruh fungsi admin. Role ini tidak dapat diubah atau dihapus melalui API.                                                                     |
| TBD (To Be Determined)                  | Hal yang belum diputuskan atau diketahui dan masih memerlukan tindak lanjut.                                                                                                     |
| Validasi (validation)                   | Pemeriksaan bahwa input dan tindakan memenuhi format, aturan, serta batas yang ditentukan.                                                                                       |
| VUser (virtual user)                    | Pengguna virtual yang disimulasikan untuk menghasilkan beban pada pengujian performa.                                                                                            |
| Visitor (pengunjung)                    | Pengguna fitur publik yang tidak sedang menggunakan dashboard administrator.                                                                                                     |
