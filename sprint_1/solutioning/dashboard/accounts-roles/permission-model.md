# Katalog Permission Game Service Platform

> **Jenis Dokumen:** Penjelasan model permission  
> **Cakupan:** Permission untuk fungsi dashboard administrator di game-service-platform  
> **Sumber implementasi:** [IFA source route Go dashboard](../../../../sprint_3/solutioning/backend-go/dashboard-source-ifa.md)

## Tujuan

Dokumen ini menjelaskan permission yang digunakan game-service-platform, cakupan fungsi yang dilindungi, dan hubungan permission dengan role. Ini adalah katalog permission, bukan kontrak endpoint. Nama permission ditulis persis seperti string yang diperiksa oleh handler Go.

## Daftar Permission

| Permission        | Cakupan fungsi                                                                                                                              | Contoh fitur/route                                                               | Status sumber                                                                                                         |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| `catalog-manage`  | Mengelola katalog game: game, kategori layanan, dan layanan. Mencakup baca serta perubahan katalog.                                         | `/api/admin/games/*`, `/api/admin/service-categories/*`, `/api/admin/services/*` | Diperiksa handler Go.                                                                                                 |
| `users-manage`    | Mengelola akun member/customer: daftar, detail, pembuatan, perubahan, dan penghapusan akun.                                                 | `/api/admin/users/*`                                                             | Diperiksa handler Go. Endpoint create saat ini mengembalikan 503 karena provider undangan belum tersedia.             |
| `settings-manage` | Mengelola konfigurasi dashboard, role/permission, dan operasi akun administrator yang tersedia di source Go.                                | `/api/admin/settings/*`, `/api/admin/roles/*`, `/api/admin/admin-users/*`        | Diperiksa handler Go. Untuk Sprint 1, akun administrator hanya boleh dibaca; create/update/delete bukan scope target. |
| `orders-manage`   | Membaca dan menjalankan operasi admin pada order, termasuk review pembayaran, penyelesaian order, dan akses bukti pembayaran yang dibatasi. | `/api/admin/orders/*` dan route bukti pembayaran admin                           | Diperiksa handler Go.                                                                                                 |
| `finance-read`    | Membaca daftar dan detail transaksi keuangan. Permission ini memberi akses baca, bukan eksekusi payout/refund.                              | `/api/admin/transactions/*`                                                      | Diperiksa handler Go.                                                                                                 |
| `dashboard-read`  | Membaca ringkasan/overview dashboard dan aktivitas admin.                                                                                   | `/api/admin/dashboard/*`; `/api/admin/activity-logs/*`                           | Diperiksa handler Go. Activity log juga menerima `settings-manage`.                                                   |

Route di atas menunjukkan kelompok fungsi. Daftar method/path dan hasil implementasi lengkap ada di [IFA source route Go dashboard](../../../../sprint_3/solutioning/backend-go/dashboard-source-ifa.md).

## Aturan Akses

1. Handler memeriksa sesi admin dan permission sebelum meneruskan request ke operasi fitur.
2. Permission adalah string literal yang peka terhadap kesesuaian nama. Implementasi dan konfigurasi role harus memakai nama persis di tabel; jangan mengganti dengan label UI seperti `catalog-management` atau `settings-management`.
3. Satu permission saat ini dapat mencakup beberapa jenis aksi. Contohnya `catalog-manage` melindungi operasi baca dan tulis katalog; belum ada permission terpisah untuk read/create/update/delete tiap domain.
4. Endpoint daftar activity log menerima `dashboard-read` **atau** `settings-manage`; salah satu cukup menurut pemeriksaan Go saat ini.
5. Super Admin adalah satu-satunya role yang dibuat pada bootstrap awal Sprint 1. Role ini memiliki akses ke seluruh fungsi admin dan bypass pemeriksaan grant permission, termasuk permission yang ditambahkan kemudian.
6. Role `SUPER_ADMIN` immutable: tidak dapat diubah, dinonaktifkan, atau dihapus melalui API maupun workflow administrator.
7. Role tambahan tidak dibuat saat bootstrap. Jika dibuat secara eksplisit kemudian, role tersebut hanya memperoleh permission yang diberikan kepadanya.
8. Akun administrator bersifat read-only pada scope Sprint 1. Fakta bahwa source saat ini punya route admin-account CRUD di bawah `settings-manage` tidak menjadikannya operasi target Sprint 1.
9. Permission yang hilang atau tidak dikenal harus ditolak. Kegagalan pemeriksaan authorization tidak boleh memberi akses (fail closed).

## Hubungan dengan Role

Permission adalah hak fungsi; role adalah kumpulan hak tersebut; akun administrator diberi role. Bootstrap Sprint 1 hanya membuat role `SUPER_ADMIN` dengan akses penuh. Penambahan role custom di kemudian hari tidak mengubah Super Admin dan tidak otomatis memberi akses penuh.

ERD untuk relasi `ADMIN_ROLE`, `PERMISSION`, `ROLE_PERMISSION`, dan `ACCOUNT_ROLE` ada di [Role ERD](../../../erd/role-erd.md). Detail alur akses setiap route ada di solutioning fitur dan IFA source.

## Batas Katalog Saat Ini

- Keenam permission di atas adalah permission yang ditemukan pada pemeriksaan handler Go.
- Belum ada granularitas khusus `game-read`, `game-write`, `role-manage`, `admin-account-read`, `admin-account-manage`, atau permission peraksi lainnya.
- `finance-read` hanya untuk pembacaan transaksi; route finansial yang mengubah transaksi/payout/refund tidak boleh diasumsikan terlindungi oleh permission ini.
- Matriks permission untuk role custom belum didefinisikan. Permission custom hanya boleh diberikan melalui alur role yang berwenang dan kebijakan least privilege.
- Super Admin memiliki akses ke seluruh fungsi admin; tetap tunduk pada autentikasi, status akun aktif, validasi request, dan aturan bisnis.

## Referensi

- [Role ERD](../../../erd/role-erd.md)
- [Current Go dashboard route IFA](../../../../sprint_3/solutioning/backend-go/dashboard-source-ifa.md)
- IFA role: `create-role.md`, `get-list-roles.md`, `get-detail-role.md`, `update-role.md`, `delete-role.md`

