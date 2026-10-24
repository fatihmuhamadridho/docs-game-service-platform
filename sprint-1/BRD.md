# JokiHub — Dokumen Kebutuhan Bisnis Sprint 1: Akses Admin dan Game

> **Status:** Baseline kebutuhan dan keputusan Sprint 1; dokumen solutioning dan ERD menjadi acuan kontrak serta model data.  
> **Sprint:** 1  
> **ID kebutuhan:** ID portofolio yang sudah ada dipertahankan untuk pelacakan.

## 1. Gambaran Proyek

Sprint 1 menghadirkan login dan session administrator, pengelolaan role dan permission, direktori administrator yang hanya-baca, serta pengelolaan dan penemuan game publik. BRD ini menjelaskan kebutuhan bisnis; payload permintaan dan perilaku teknis dijelaskan dalam dokumen solutioning tiap fitur.

## 2. Tujuan

- Melindungi akses dashboard melalui role Super Admin dan role kustom dengan permission yang sesuai.
- Memungkinkan administrator berwenang meninjau akun administrator tanpa mengubahnya melalui dashboard.
- Memungkinkan administrator berwenang mengelola katalog game serta membantu pengunjung menemukan game aktif.

## 3. Cakupan dan Urutan

### Prasyarat

Dashboard menggunakan login administrator Sprint 1 dengan username atau email dan password. SMTP, login pelanggan, registrasi pelanggan, dan reset password melalui email tidak termasuk Sprint 1.

### Urutan Pelaksanaan

1. Menghadirkan login administrator dan pengelolaan session aman. Provisioning akun/credential tetap dilakukan melalui proses backend, basis data, atau skrip terkendali.
2. Menghadirkan pengelolaan role dan permission. Bootstrap membuat tepat satu role aktif `SUPER_ADMIN`, menugaskannya kepada akun Super Admin, dan memberinya akses ke seluruh fungsi admin. Role sistem ini tidak dapat diubah, dinonaktifkan, atau dihapus melalui API dashboard. Role kustom dibuat secara eksplisit setelah bootstrap.
3. Menghadirkan halaman daftar dan detail akun administrator. Akses dashboard hanya-baca; penyiapan akun dilakukan melalui proses backend, basis data, atau skrip yang terkendali. Setiap administrator memiliki satu role aktif.
4. Menghadirkan operasi buat, daftar, detail, ubah, dan hapus game di dashboard.
5. Menghadirkan daftar game aktif dan game populer untuk publik. `isActive` menentukan apakah game terlihat publik.

### Di Luar Cakupan Sprint 1

- Login pelanggan, registrasi anggota/pelanggan, reset password melalui email, undangan email, dan integrasi SMTP.
- Membuat, mengubah, atau menghapus akun administrator melalui dashboard; undangan email dan integrasi penyedia email.
- Pengelolaan data anggota/pelanggan dan login pelanggan.
- Pengelolaan kategori dan layanan, harga layanan, serta penemuan kategori/layanan publik.
- Data rating dan jumlah ulasan game, integrasi penyedia rating, serta pengurutan berdasarkan rating.
- Proses pemesanan, pembayaran, pesanan, pemenuhan pesanan, pengembalian dana, pencairan dana, dan fitur operasional dashboard.

## 4. Kebutuhan Bisnis

| ID     | Kebutuhan                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| BR-001 | Sebagai pengunjung, saya ingin melihat game aktif yang disetujui untuk dipublikasikan, termasuk kumpulan game populer, agar dapat menemukan game yang ditawarkan JokiHub. Respons publik menyediakan identitas dan data tampilan game, termasuk URL gambar wajib dan platform bila nilai platform tersedia pada game.                                                                                                                                     |
| BR-002 | Sebagai pengunjung, saya ingin mencari dan memfilter katalog game publik serta berpindah antarhalaman agar dapat menemukan game yang relevan. Sprint 1 mendukung pencarian nama/slug, filter platform `MOBILE`, `PC`, atau `CONSOLE`, pengurutan default `SORT_ORDER_ASC`, pilihan `POPULAR` dan `NAME_ASC`, serta paginasi. Endpoint game populer menampilkan game berflag populer dalam urutan `sortOrder` ascending. Pengurutan rating tidak termasuk. |
| BR-003 | Sebagai administrator berwenang, saya ingin melihat akun administrator beserta satu role yang ditugaskan pada setiap akun agar akses istimewa dapat ditinjau. Dashboard hanya menyediakan daftar dan detail; penyiapan Super Admin dilakukan melalui bootstrap backend, basis data, atau skrip yang terkendali.                                                                                                                                           |
| BR-004 | Sebagai administrator berwenang, saya memerlukan kontrol role dan permission agar akses diterapkan secara konsisten. Bootstrap membuat satu role aktif `SUPER_ADMIN` yang immutable dan berakses penuh. Role kustom memakai status `ACTIVE` atau `INACTIVE`, default `ACTIVE`; perubahan permission berlaku pada request berikutnya. Super Admin dapat memilih permission yang tersedia di katalog sistem.                                                |
| BR-005 | Sebagai administrator berwenang, saya ingin membuat, melihat daftar, melihat detail, mengubah, dan menghapus game agar katalog publik mencerminkan penawaran yang disetujui. Sprint 1 hanya mencakup game; kategori dan layanan berada di luar sprint ini.                                                                                                                                                                                                |
| BR-006 | Sebagai pemilik bisnis, saya ingin operasi publik dan administratif memvalidasi input serta menerapkan aturan akses yang sesuai agar permintaan tidak valid atau tanpa izin tidak mengubah data terlindungi maupun membuka informasi privat.                                                                                                                                                                                                              |
| BR-007 | Sebagai administrator, saya ingin masuk ke dashboard dengan username atau email dan password agar hanya akun admin aktif yang dapat mengakses fungsi Sprint 1. Backend menerbitkan dan memperbarui session melalui cookie aman; SMTP dan login pelanggan tidak dibutuhkan.                                                                                                                                                                                |
| BR-008 | Sebagai konsumen API dashboard, saya ingin endpoint detail role dan game memakai pola action-based `/detail/{id}` secara konsisten agar kontrak Sprint 1 mudah dipahami dan seragam.                                                                                                                                                                                                                                                                      |

## 5. Aturan Bisnis dan Dependensi

- **Autentikasi admin:** Operasi dashboard memerlukan admin terautentikasi dan berwenang. Login menerima username/email dan password; backend mengatur access JWT 15 menit dan refresh token opaque 7 hari melalui cookie `HttpOnly; Secure; Path=/; SameSite=None`. Refresh token dirotasi setiap pemakaian. CORS memakai allowlist origin dashboard dengan credentials; request berbasis cookie memakai CSRF token. Login/refresh dibatasi 20 request per IP per 15 menit; login juga dibatasi 5 kegagalan per identifier setelah trim + lowercase per 15 menit.
- **Role:** Hanya status `ACTIVE` dan `INACTIVE` yang berlaku. Role kustom default `ACTIVE`; `SUPER_ADMIN` immutable. Role kustom tidak dapat dinonaktifkan selama masih ditugaskan ke akun aktif. Perubahan role/permission langsung berlaku pada request berikutnya; API membaca state otorisasi terkini dan tidak menggunakan cache permission yang stale.
- **Daftar role:** Pencarian mencocokkan `name` dan `code` tanpa membedakan kapitalisasi. `page` default 1; `limit` adalah jumlah record per halaman (bukan batas panjang teks pencarian), default 10 dan maksimum 100. Panjang teks pencarian role maksimum 100 karakter. Sort `sortBy` mendukung `name`, `createdAt`, dan `updatedAt`; `sortOrder` mendukung `ASC`/`DESC`. Default `createdAt DESC`, lalu `id ASC` sebagai tie-break.
- **Permission:** Super Admin dapat memilih permission aktif apa pun dalam katalog sistem untuk role kustom. Definisi permission dikelola sistem, bukan melalui CRUD role.
- **Akun admin:** Daftar/detail admin bersifat read-only di dashboard. Daftar memakai default `page=1`, `limit=20`, maksimum `limit=100`. Provisioning, offboarding, dan assignment role ke akun dilakukan melalui proses backend/database/script terkendali; dashboard Sprint 1 tidak menyediakan CRUD akun atau endpoint assignment role. Akun `INACTIVE` tidak dapat login dan session aktifnya dicabut; tiap admin memiliki satu role aktif. Akun soft-deleted tidak dapat login dan dikecualikan dari daftar/detail; record akun dan credential dipertahankan dua bulan sebelum purge permanen, tanpa API pemulihan. Session dicabut saat offboarding dan tiap generasi dibersihkan setelah masa berlaku tokennya lewat. Hash Argon2id disimpan di `ADMIN_CREDENTIAL`; session dan digest refresh disimpan di `ADMIN_SESSION`.
- **ID:** ID entitas domain memakai prefix `admin_`, `role_`, `game_`, dan `permission_`. ID record internal memakai `credential_`, `session_`, dan `account_role_` serta tidak dikembalikan oleh API. ID game memakai `game_` diikuti ULID kanonis 26 karakter Crockford Base32 huruf kapital; contoh `game_01ARZ3NDEKTSV4RRFFQ69G5FAV`.
- **Daftar game dashboard:** Default `page=1`, `limit=20`, maksimum `limit=100`.
- **Katalog game:** Dokumen game berisi `name`, `slug`, `description`, `imageUrl`, `platform`, `isActive`, `isPopular`, `sortOrder`, dan timestamp. `description` adalah deskripsi game yang dikelola melalui API game dashboard sesuai permission create/update, opsional, maksimum 500 karakter. Hanya Super Admin yang dapat mengubah `isActive` dan `isPopular` secara langsung; soft delete otomatis mengatur keduanya menjadi `false` sebagai efek lifecycle. `platform` opsional: `MOBILE`, `PC`, atau `CONSOLE`. `isActive` dan `isPopular` default `false`; `isPopular=true` hanya valid untuk game aktif. `imageUrl` wajib URL HTTPS absolut; Sprint 1 tidak membatasi host.
- **Visibilitas dan urutan publik:** Hanya game `isActive=true` dan belum dihapus yang terlihat publik; endpoint populer juga memerlukan `isPopular=true`. Default katalog publik dan endpoint populer diurutkan berdasarkan `sortOrder ASC`. Daftar game publik memakai default `page=1`, `limit=20`, maksimum `limit=100`; daftar populer memakai default `page=1`, `limit=6`, maksimum `limit=50`. Sort katalog `NAME_ASC` mengurutkan nama ascending. Rating dan review count tidak dikembalikan. Belum ada dukungan multi-bahasa; `Accept-Language` diabaikan dan konten memakai bahasa Indonesia. Query parameter yang tidak dikenal diabaikan.
- **Penghapusan:** Endpoint DELETE role/game tidak memerlukan body; body kosong diterima dan body nonkosong ditolak dengan HTTP 400. Penghapusan role/game memakai soft delete dengan `deleted_at`; item terhapus langsung dikecualikan dari pembacaan dan tidak memiliki API pemulihan. Data dan relasi terkait dipertahankan 2 bulan, lalu dihapus permanen oleh proses retensi. Penghapusan role ditolak selama masih ada assignment akun aktif. `SUPER_ADMIN` tidak dapat dihapus atau dinonaktifkan. Penghapusan akun admin hanya melalui proses backend terkendali; akun dan credential terkait mengikuti retensi dua bulan, sedangkan session dicabut saat offboarding dan dibersihkan TTL setelah masa berlaku token lewat.
- **Concurrency mutasi:** Sprint 1 tidak memakai optimistic locking (`ETag`, `If-Match`, atau version field); perubahan efektif terakhir yang berhasil memakai last-write-wins dan memperbarui `updatedAt`.
- **Retry mutasi:** Sprint 1 tidak memakai `Idempotency-Key`. Create dengan key bisnis duplikat menghasilkan HTTP 409 dan tidak membuat record kedua. Update yang nilai efektifnya sama dengan data tersimpan mengembalikan sukses tanpa menulis perubahan atau mengubah `updatedAt`. Delete berulang pada tombstone dalam masa retensi mengembalikan sukses yang sama; ID yang tidak ada atau sudah dipurge mengembalikan HTTP 404.
- **Exposure dan HTTP:** Route dashboard dapat dijangkau dari internet, tetapi wajib session admin, permission, CORS allowlist, dan CSRF; route katalog publik tidak memerlukan autentikasi. HTTP mapping: 400 input invalid, 401 belum terautentikasi, 403 tidak berwenang, 404 resource tidak ditemukan/terhapus, 409 konflik state/unik, 429 rate limit, 500 kegagalan teknis. Create, update, dan delete yang berhasil mengembalikan HTTP 200 dengan envelope `{code, message, data}` dan `data: null`.
- **Rate limit API selain autentikasi:** Jendela bergulir 1 menit. Route baca admin: maksimum 1.200 request/IP dan 600 request/admin. Mutasi role/game: maksimum 1.200 request/IP dan 60 request/admin. Katalog publik: maksimum 1.200 request/IP. Pelampauan mengembalikan HTTP 429 dengan `Retry-After` dalam detik.
- **Teknologi:** Next.js untuk web, Go untuk API, MongoDB untuk penyimpanan, Vercel untuk deployment web dan API, sesuai [HLD Sprint 1](./hld/sprint-1-hld.md).

## 6. SLA Awal

Target awal untuk operasi API dashboard, autentikasi admin, dan katalog publik Sprint 1. Target beban dan kapasitas berikut merupakan asumsi, bukan hasil benchmark; validasi melalui uji beban sebelum rilis.

| Metrik                      | Target                                                                                                                          |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| Ketersediaan bulanan        | 99,9%                                                                                                                           |
| Beban uji beban             | 50 VUser dan 20 permintaan/detik dengan campuran endpoint yang representatif                                                    |
| Latensi baca                | p95 ≤ 500 ms; p99 ≤ 2 detik                                                                                                     |
| Latensi tulis               | p95 ≤ 1 detik; p99 ≤ 2 detik                                                                                                    |
| Kapasitas awal per instance | 2 vCPU, RAM 4 GiB; CPU ≤ 70% dan memori ≤ 75% pada beban target                                                                 |
| Kesalahan server            | ≤ 0,1% permintaan valid per bulan; timeout dan HTTP 5xx dihitung, sedangkan 4xx validasi/otorisasi yang diharapkan dikecualikan |
