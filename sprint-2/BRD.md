# JokiHub — Dokumen Kebutuhan Bisnis Sprint 2: Katalog, Pengaturan, dan Audit

> **Status:** Draft — cakupan dan urutan merupakan usulan, belum menjadi komitmen delivery.
> **Sprint:** 2
> **ID kebutuhan:** Sprint 2 BR-003–BR-005, BR-019, BR-020, BR-022. ID ini tetap berscope sprint hingga register portofolio kanonis tersedia (D-17). Dokumen ini mencakup kebutuhan yang tersedia dalam repo; pembuatan acceptance criteria dan pemetaan ke backlog story tidak termasuk deliverable saat ini.

## 1. Gambaran Proyek

Sprint 2 menindaklanjuti katalog game yang tersedia dengan kebutuhan penemuan kategori dan layanan, pengelolaan kategori dan layanan, pengaturan platform, serta pencatatan audit administratif. Sumber repo juga mencatat route data public settings yang terpisah dari delivery halaman publik. Keberadaan route maupun prototype UI tidak dengan sendirinya menyetujui exposure, persistensi, atau perilaku target.

## 2. Tujuan

- Membantu pengunjung menemukan kategori dan layanan yang memenuhi aturan publikasi serta harga yang disetujui.
- Memungkinkan administrator berwenang mengelola kategori dan layanan katalog.
- Memungkinkan administrator berwenang memperbarui pengaturan platform yang disetujui.
- Mencatat aksi administratif sebagai audit event sesuai kebijakan yang disetujui.

## 3. Cakupan dan Urutan

### Prasyarat

- Fondasi game dan akses administrator berasal dari Sprint 1. Permission kategori dan layanan perlu keputusan katalog/grant Sprint 2 (D-18); pengelolaan role Sprint 1 tidak diubah.
- Eligibility publik, lifecycle kategori/layanan, serta aturan harga tetap dan quote perlu disetujui sebelum katalog dianggap siap untuk pemilihan order (D-02/D-03/D-14).
- Field, akses, dan exposure pengaturan publik perlu keputusan tersendiri dari keberadaan route settings (D-05/D-09).
- Audit lintas aksi memerlukan keputusan event boundary, taxonomy, redaksi, retensi, delivery, dan perilaku kegagalan (D-07/D-08).

### Urutan Pelaksanaan

1. Penemuan kategori publik dan pengelolaan kategori bergantung pada data game yang tersedia serta aturan eligibility yang disetujui.
2. Penemuan dan pengelolaan layanan bergantung pada hubungan game/kategori, aturan eligibility, dan keputusan harga/quote.
3. Pengaturan platform dilaksanakan setelah field, permission, public projection, dan semantik maintenance/registrasi disetujui.
4. Fondasi audit administratif mencakup aksi yang ditetapkan setelah taxonomy, redaksi, retensi, dan kebijakan kegagalan disetujui.
5. Delivery halaman help/about/home, halaman konten settings, dan state access-denied/not-found hanya masuk bila Product Owner mengonfirmasi scope serta kontennya (D-01). Route data `GET /api/settings/v1/public` yang tercatat di repo adalah interface terpisah; keberadaannya tidak menyetujui delivery halaman atau exposure target settings (D-09).

### Di Luar Cakupan Sprint 2

Item berikut hanya kandidat, belum dinyatakan sebagai scope atau pengecualian: pemulihan password pelanggan; review/penyelesaian pembayaran oleh admin; fungsi tambahan yang hanya terlihat pada UI/mock; serta refund/payout, settlement, rekonsiliasi, dan fulfillment. Dokumen Sprint 3 menempatkan checkout/pembayaran, pelacakan order, dan consumer dashboard/read di Sprint 3. Product owner perlu menetapkan disposition kandidat dan workflow yang beririsan dengan Sprint 2 (D-13); pembuatan acceptance criteria tidak termasuk deliverable saat ini.

## 4. Kebutuhan Bisnis

| ID     | Kebutuhan                                                                                                                                                                                                                                   |
| ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| BR-003 | Sebagai pengunjung, saya perlu melihat kategori layanan aktif agar dapat menavigasi berdasarkan jenis layanan. Kategori tanpa layanan publik yang memenuhi syarat tidak boleh menghasilkan pilihan pembelian kosong.                        |
| BR-004 | Sebagai pelanggan, saya perlu melihat layanan aktif untuk game yang ditentukan oleh slug halaman order agar dapat memilih layanan yang valid.                                                                                               |
| BR-005 | Sebagai pelanggan, saya perlu membedakan layanan harga tetap dari layanan yang memerlukan penawaran harga agar tidak ditampilkan harga rekaan atau membayar penawaran yang belum disetujui.                                                 |
| BR-019 | Sebagai administrator berwenang, saya perlu mengelola kategori dan layanan agar katalog publik mencerminkan penawaran yang disetujui. Pengelolaan game tetap pada Sprint 1. Entri nonaktif atau terhapus tidak ditawarkan untuk order baru. |
| BR-020 | Sebagai administrator berwenang, saya perlu memperbarui informasi platform, kontak, preferensi regional, mode pemeliharaan, dan ketersediaan registrasi member yang disetujui. Exposure publik settings diputuskan terpisah.                |
| BR-022 | Sebagai bisnis, saya memerlukan aksi administratif dicatat sebagai audit event append-only yang berisi aktor, aksi, target, hasil, dan waktu. Secret dan data personal atau pembayaran yang tidak diperlukan harus diredaksi.               |

## 5. Aturan Bisnis dan Dependensi

- Data game yang tersedia mendahului pengelolaan kategori/layanan; aturan eligibility katalog dan harga mendahului pemilihan layanan publik.
- Fixed-price dan quote-required adalah kondisi berbeda. Nominal quote yang belum disetujui tidak boleh ditampilkan sebagai harga final atau digunakan untuk pembayaran (BR-005).
- Pengelolaan admin memerlukan otorisasi. Model permission target kategori/layanan dan grant role menunggu keputusan D-18; dokumen route-to-handler coverage dalam repo mencatat `catalog-manage`, sedangkan IFA mengusulkan key granular. Catatan tersebut adalah bukti dokumenter lokal dan belum diverifikasi ulang terhadap kode pada pekerjaan ini.
- Aksi administratif yang termasuk BR-022 harus mempertahankan identitas logis aktor dan target, aksi, hasil, serta waktu pada event tersimpan. Field tambahan, actor sistem, anonimisasi, redaksi, event boundary, retensi, storage, dan perilaku kegagalan menunggu kebijakan owner (D-07/D-08).
- Route admin settings yang tercatat di repo mengembalikan/mengubah sepuluh field dengan permission `settings-manage`; fakta route tidak menyetujui schema fisik, public allowlist target, maintenance/registration semantics, concurrency, atau propagation.
- Checkout Sprint 3 memerlukan instruksi pembayaran pelanggan terkini. Owner dan source of truth belum ditetapkan; jangan anggap instruksi tersebut otomatis bagian dari BR-020 settings (D-16).
- Dokumen ini mencakup enam BR-ID yang tersedia untuk Sprint 2. Acceptance criteria dan pemetaan story tidak termasuk deliverable saat ini; ID portofolio lintas sprint masih menunggu register kanonis (D-17).
- [Register keputusan terbuka Sprint 2](./solutioning/open-decisions.md) mendefinisikan D-01–D-18 yang dirujuk dokumen scope dan solutioning.
- Owner, estimasi, tanggal, metrik keberhasilan, dan target SLA delivery masih TBD.

## 6. SLA Awal

Belum ada target SLA Sprint 2 yang disetujui atau hasil benchmark yang tersedia. Setiap IFA memuat usulan baseline numerik turunan Sprint 1 menurut jenis operasi; angka tersebut bukan komitmen Sprint 2. Cakupan endpoint, criticality, forecast beban, kondisi ukur, sumber angka, dan persetujuan product/platform harus dikonfirmasi sebelum baseline diadopsi (D-12).

| Metrik                                   | Target                                                                                                       |
| ---------------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| Ketersediaan layanan publik dan admin    | TBD — IFAs mengusulkan 99.9% bulanan; tetapkan criticality dan setujui per layanan.                          |
| Beban dan kapasitas                      | TBD — IFAs mengusulkan 50 VUser / 20 request/s dan 2 vCPU / 4 GiB; konfirmasi forecast dan hasil NFT.        |
| Latensi API publik dan admin             | TBD — IFAs mengusulkan read p95 ≤ 500 ms / p99 ≤ 2 s dan write p95 ≤ 1 s / p99 ≤ 2 s; tetapkan per endpoint. |
| Propagasi settings dan freshness katalog | TBD — tetapkan definisi ukur, kebijakan cache, dan target operasional.                                       |
