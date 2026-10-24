# Katalog Permission Dashboard — Sprint 1

> **Jenis dokumen:** Katalog permission dan kebijakan otorisasi
> **Cakupan:** Role, akun administrator, dan game dalam Sprint 1
> **Status:** Katalog permission Sprint 1; Super Admin dapat memberi permission katalog kepada role kustom.

## Tujuan

Dokumen ini mendefinisikan kemampuan admin yang dilindungi permission pada Sprint 1. Dokumen ini bukan IFA dan tidak menetapkan kontrak endpoint. Nama permission di bawah adalah key kanonis yang digunakan pada kontrak API Sprint 1.

## Katalog Permission Sprint 1

| Permission key       | Kemampuan yang diberikan                                                   | Batas Sprint 1                                                                                                                                    |
| -------------------- | -------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| `role.read`          | Melihat daftar dan detail role beserta permission yang terkait.            | Mencakup pembacaan role; tidak memberi hak perubahan.                                                                                             |
| `role.create`        | Membuat custom role dan memilih permission dari katalog sistem.            | Role tambahan dibuat eksplisit setelah bootstrap; tidak dibuat otomatis.                                                                          |
| `role.update`        | Mengubah metadata atau permission custom role.                             | Tidak dapat mengubah atau menonaktifkan `SUPER_ADMIN`.                                                                                            |
| `role.delete`        | Menghapus custom role sesuai aturan assignment dan lifecycle role.         | Tidak dapat menghapus `SUPER_ADMIN`.                                                                                                              |
| `admin-account.read` | Melihat daftar dan detail akun administrator beserta role yang ditugaskan. | Read-only. Tidak mencakup pembuatan, perubahan, atau penghapusan akun.                                                                            |
| `game.read`          | Melihat daftar dan detail game di dashboard admin.                         | Permission untuk route admin; katalog publik membaca game aktif tanpa grant akun admin.                                                           |
| `game.create`        | Membuat data game.                                                         | Hanya pengelolaan game; kategori layanan dan layanan masuk Sprint 2.                                                                              |
| `game.update`        | Mengubah data game.                                                        | Perubahan `isActive` dan `isPopular` hanya dapat dilakukan Super Admin.                                                                           |
| `game.delete`        | Menghapus data game secara soft delete.                                    | Mengisi `deleted_at`; lifecycle soft delete otomatis menonaktifkan game dan membersihkan flag populer. Bukan hak untuk mengubah kategori/layanan. |

Key permission pada tabel adalah identifier stabil yang dipakai API dalam array `permissions`. ID internal entitas permission memakai prefix `permission_`; key seperti `game.read` bukan ID record. Super Admin dapat memberikan seluruh key aktif pada katalog kepada role kustom. API memvalidasi permission key terhadap katalog ini.

## Kebijakan Role dan Grant

1. Bootstrap awal membuat tepat satu role aktif, `SUPER_ADMIN`, dan menugaskannya ke akun Super Admin melalui proses backend/database/script yang terkontrol.
2. `SUPER_ADMIN` memiliki akses ke seluruh fungsi admin Sprint 1 dan fungsi admin yang ditambahkan kemudian. Hak penuhnya tidak bergantung pada daftar grant permission.
3. `SUPER_ADMIN` adalah role system yang immutable. API dashboard tidak dapat mengubah, menonaktifkan, atau menghapus role tersebut.
4. Bootstrap tidak membuat role custom. Custom role hanya dibuat secara eksplisit setelah bootstrap melalui flow yang berwenang.
5. Super Admin dapat memberikan atau mencabut permission aktif apa pun dari katalog sistem pada role kustom. Permission yang tidak ada atau tidak aktif tetap ditolak.
6. Permission tidak dikenal atau grant yang tidak tersedia ditolak secara default (fail closed). Validasi permission dan otorisasi dilakukan sebelum data terlindungi dibaca atau diubah.
7. Permintaan tanpa sesi valid, tanpa permission, atau dengan input tidak valid ditolak tanpa mengubah data terlindungi.

## Batas Scope Sprint 1

- Akun administrator hanya dapat dibaca melalui dashboard. Provisioning Super Admin berlangsung lewat bootstrap terkontrol, bukan CRUD dashboard.
- Permission member/customer, order, transaksi/finance, payout, refund, activity log, FAQ, banner, dan fitur operasional lain tidak termasuk katalog Sprint 1.
- Permission pengelolaan kategori layanan dan layanan tidak termasuk; bagian tersebut dilanjutkan pada Sprint 2.
- Permission untuk route katalog game publik tidak diberikan sebagai grant role admin. Endpoint publik hanya menyajikan game yang memenuhi aturan publikasi.
- Katalog permission bukan antarmuka CRUD permission. Penambahan atau perubahan definisi permission memerlukan perubahan solutioning dan persetujuan pemilik akses.

## Hubungan dengan Role dan Data

Permission adalah hak untuk menjalankan kemampuan; role mengelompokkan permission; akun administrator menerima role. Model relasi `ADMIN_ROLE`, `PERMISSION`, `ROLE_PERMISSION`, dan `ACCOUNT_ROLE` dijelaskan di [Role ERD](../../../erd/role-erd.md). Kebijakan awal `SUPER_ADMIN` dan bootstrap-nya dijelaskan di [BRD Sprint 1](../../../BRD.md).

## Pengelolaan Katalog Permission

- API memvalidasi hak akses menggunakan key aktif pada katalog ini.
- Katalog permission dikelola oleh sistem, bukan melalui CRUD role. Hanya permission aktif dari katalog yang dapat ditetapkan pada role kustom.
- Role ERD memisahkan permission_id berprefix permission_ sebagai ID record dari permission_key seperti game.read yang digunakan API; relasi grant menyimpan ID record.
- Role dan game dihapus secara soft delete melalui `deleted_at`; `SUPER_ADMIN` tetap immutable.

## SLA Baseline

Evaluasi permission termasuk dalam latency dan availability route dashboard pada [BRD Sprint 1](../../../BRD.md): availability 99,9% per bulan, 50 VUser / 20 request per detik, baca p95 ≤ 500 ms dan tulis p95 ≤ 1 detik; p99 ≤ 2 detik. Katalog ini tidak menetapkan SLA route tersendiri.

## Referensi

- [BRD Sprint 1](../../../BRD.md)
- [Role ERD](../../../erd/role-erd.md)
- [Create Role IFA](./create-role.md)
- [List Roles IFA](./get-list-roles.md)
- [Role Detail IFA](./get-detail-role.md)
- [Update Role IFA](./update-role.md)
- [Delete Role IFA](./delete-role.md)
- [List Admin Accounts IFA](../admin-accounts/get-list-admin-accounts.md)
- [Admin Account Detail IFA](../admin-accounts/get-admin-account-detail.md)
- [List Games IFA](../games/get-list-games.md)
- [Create Game IFA](../games/create-game.md)
- [Update Game IFA](../games/update-game.md)
- [Delete Game IFA](../games/delete-game.md)
