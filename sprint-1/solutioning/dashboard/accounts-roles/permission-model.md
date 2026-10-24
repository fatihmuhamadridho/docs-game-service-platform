# Katalog Permission Dashboard — Sprint 1

> **Jenis dokumen:** Katalog permission dan kebijakan otorisasi
> **Cakupan:** Role, akun administrator, dan game dalam Sprint 1
> **Status:** Katalog fungsional untuk scope Sprint 1; matriks grant custom role menunggu persetujuan Security/Product.

## Tujuan

Dokumen ini mendefinisikan kemampuan admin yang dilindungi permission pada Sprint 1. Dokumen ini bukan IFA dan tidak menetapkan kontrak endpoint. Nama permission di bawah adalah identifier usulan untuk menyamakan solutioning; implementasi harus memakai identifier yang disepakati bersama pemilik API sebelum rilis.

## Katalog Permission Sprint 1

| Permission usulan    | Kemampuan yang diberikan                                                   | Batas Sprint 1                                                                              |
| -------------------- | -------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| `role-read`          | Melihat daftar dan detail role beserta permission yang terkait.            | Mencakup pembacaan role; tidak memberi hak perubahan.                                       |
| `role-create`        | Membuat custom role dan memberikan permission yang boleh didelegasikan.    | Role tambahan dibuat eksplisit setelah bootstrap; tidak dibuat otomatis.                    |
| `role-update`        | Mengubah metadata atau permission custom role.                             | Tidak dapat mengubah atau menonaktifkan `SUPER_ADMIN`.                                      |
| `role-delete`        | Menghapus custom role sesuai aturan assignment dan lifecycle role.         | Tidak dapat menghapus `SUPER_ADMIN`.                                                        |
| `admin-account-read` | Melihat daftar dan detail akun administrator beserta role yang ditugaskan. | Read-only. Tidak mencakup pembuatan, perubahan, atau penghapusan akun.                      |
| `game-read`          | Melihat daftar dan detail game di dashboard admin.                         | Permission untuk route admin; katalog publik membaca game aktif tanpa grant akun admin.     |
| `game-create`        | Membuat data game.                                                         | Hanya pengelolaan game; kategori layanan dan layanan masuk Sprint 2.                        |
| `game-update`        | Mengubah data game.                                                        | Mengikuti aturan lifecycle/publikasi game Sprint 1.                                         |
| `game-delete`        | Menghapus atau menonaktifkan data game sesuai aturan lifecycle.            | Perilaku penghapusan mengikuti IFA game terkait; bukan hak untuk mengubah kategori/layanan. |

Identifier tersebut memisahkan hak baca dan perubahan agar role custom dapat menerima akses sesuai kebutuhan. Identifier ini bukan klaim bahwa seluruh string sudah diterapkan pada handler Go saat ini. IFA tiap operasi tetap menjadi sumber perilaku endpoint dan validasinya.

## Kebijakan Role dan Grant

1. Bootstrap awal membuat tepat satu role aktif, `SUPER_ADMIN`, dan menugaskannya ke akun Super Admin melalui proses backend/database/script yang terkontrol.
2. `SUPER_ADMIN` memiliki akses ke seluruh fungsi admin Sprint 1 dan fungsi admin yang ditambahkan kemudian. Hak penuhnya tidak bergantung pada daftar grant permission.
3. `SUPER_ADMIN` adalah role system yang immutable. API dashboard tidak dapat mengubah, menonaktifkan, atau menghapus role tersebut.
4. Bootstrap tidak membuat role custom. Custom role hanya dibuat secara eksplisit setelah bootstrap melalui flow yang berwenang.
5. Matriks permission untuk custom role, termasuk hak delegasi dan siapa yang boleh memberikan setiap permission, belum disetujui. Jangan menganggap custom role memiliki akses sampai grant disetujui dan diberikan.
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

## Keputusan yang Masih Diperlukan

- Persetujuan identifier final permission dan konsistensinya dengan pemeriksaan authorization pada Go API.
- Matriks permission yang boleh diberikan ke custom role serta aturan delegasinya.
- Apakah permission role dikelola sebagai katalog yang di-seed oleh backend atau sumber konfigurasi lain.
- Aturan lifecycle game yang menentukan perilaku `game-delete`.

Keputusan tersebut tidak mengubah aturan yang sudah ditetapkan: bootstrap hanya membuat `SUPER_ADMIN`, role itu memiliki akses penuh dan immutable, akun administrator dashboard bersifat read-only, dan scope admin katalog Sprint 1 berfokus pada game.

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
