# Admin Dashboard - Update Service Interface Agreement

> **Document Type:** IFA / API Contract  
> **Status:** Draft  
> **Service / Domain:** Admin Dashboard — Game Service Management  
> **Consumer / Partner:** JokiHub Admin Dashboard (authorized administrator)

## Design

![Sprint 2 dashboard design](../../../design/dashboard.png)

## Sequence Diagram

```mermaid
sequenceDiagram
    actor Admin as Administrator
    participant UI as Admin Dashboard UI
    participant API as Game Service Management API
    participant DB as Catalog Database
    participant Audit as Audit policy (TBD)
    Admin->>UI: Edit layanan dan simpan perubahan
    UI->>API: PUT /api/admin/services/v1/update/{serviceId} dengan payload dan kredensial admin
    API->>API: Autentikasi dan otorisasi izin service-update
    alt Tidak terautentikasi atau tidak berizin
        API-->>UI: 400 SRVB01001 / SRVB01002, data null
        UI-->>Admin: Tampilkan pesan akses
    else Berizin
        API->>API: Validasi serviceId, field, dan kombinasi harga
        alt Identifier atau payload tidak valid
            API-->>UI: 400 SRVB02001, data null
            UI-->>Admin: Tampilkan kesalahan field
        else Request valid
            API->>DB: Ambil service; validasi game/kategori dan eligibility status
            alt Service tidak ditemukan atau soft-deleted
                DB-->>API: Service tidak tersedia untuk update
                API-->>UI: 400 SRVB02003, data null
                UI-->>Admin: Tampilkan layanan tidak ditemukan
            else Game/kategori tidak valid atau tidak eligible
                DB-->>API: Parent reference invalid/inactive
                API-->>UI: 400 SRVB02002, data null
                UI-->>Admin: Tampilkan referensi tidak dapat digunakan
            else Referensi valid
                API->>DB: Simpan perubahan game_service setelah validasi referensi
                alt Update berhasil
                    DB-->>API: Service terbaru
                    Note over API,Audit: Jalur sukses 200 hanya berlaku setelah kebijakan D-07/D-08 disetujui dan outcome audit memenuhi kebijakan; detail delivery tidak ditetapkan di sini
                    API-->>UI: 200 SRVS00000, data (bersyarat pada kebijakan audit yang disetujui)
                    UI-->>Admin: Tampilkan konfirmasi dan data terbaru setelah outcome sukses terkonfirmasi
                else Database gagal
                    DB-->>API: Kegagalan update
                    API-->>UI: 500 SRVT03001, data null
                    UI-->>Admin: Tampilkan kegagalan dan opsi coba lagi
                end
            end
        end
    end
```

## Performance / SLA Targets

> Status usulan: angka baseline turunan Sprint 1; belum menjadi komitmen Sprint 2. Forecast, kondisi ukur, dan persetujuan owner masih diperlukan (D-12).

| Success Rate                                                  | VUser                              | CPU / Memory                                                                | Response Time              |
| ------------------------------------------------------------- | ---------------------------------- | --------------------------------------------------------------------------- | -------------------------- |
| 99.9% monthly availability; valid-request server error ≤ 0.1% | 50 VUser / 20 request/s NFT target | Initial 2 vCPU / 4 GiB per instance; CPU ≤ 70%, memory ≤ 75% at target load | Write p95 ≤ 1 s; p99 ≤ 2 s |

## General Information

| Component          | Value                                                                                                                                      |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------ |
| Description        | Memperbarui relasi game/kategori, nama, harga, estimasi, dan status layanan.                                                               |
| Resource URL       | `https://{host}/api/admin/services/v1/update/{serviceId}`                                                                                  |
| Transport Protocol | HTTPS, REST                                                                                                                                |
| Request Verb       | PUT                                                                                                                                        |
| Content Type       | `application/json`                                                                                                                         |
| Interaction Type   | Synchronous                                                                                                                                |
| Security Policy    | Admin authentication dan izin `service-update`. Identity provider dan permission final TBD. Terapkan rate limiting dan audit actor/action. |

### Path / Endpoint

| Section                  | Value                                           |
| ------------------------ | ----------------------------------------------- |
| Host / Domain            | `{host}` (environment-specific)                 |
| Service Name             | Game Service Management                         |
| Version                  | `v1`                                            |
| Resource                 | `/admin/services/v1/update/{serviceId}`         |
| Proposed target endpoint | `PUT /api/admin/services/v1/update/{serviceId}` |

### API Versioning

- **Inventory status:** Method/path yang sama tercatat pada route inventory repo ini; bagian ini bukan klaim bahwa route belum ada.
- **Target contract:** IFA mengusulkan perilaku pada `PUT /api/admin/services/v1/update/{serviceId}`; payload semantics, permission, response, dan status target belum disetujui terhadap inventory.
- **Versioning:** Tetap `v1` pada usulan ini; perubahan kompatibilitas perlu keputusan API owner.
- **Proposed target endpoint:** `PUT /api/admin/services/v1/update/{serviceId}`
- **Backward Compatibility:** Consumer belum dinilai. Penambahan field hanya dapat dianggap kompatibel setelah perilaku consumer diverifikasi; perubahan field/semantik/status yang mematahkan consumer memerlukan keputusan versioning API owner.
- **Reason:** Admin perlu mengubah semua atribut layanan melalui form edit yang mengirim keseluruhan model.

## Business Rules

1. Session cookie harus valid; `Origin` harus exact-allowlisted; token CSRF dan permission `service-update` harus valid. Role mapping dan HTTP/service-code mapping auth tetap mengikuti API standard yang disetujui.
2. `serviceId` adalah ID immutable `game_service.service_id`; body tidak boleh mengubah ID, `createdAt`, `updatedAt`, atau `deletedAt`.
3. Hanya layanan yang ada dan belum soft-deleted dapat diperbarui. Layanan aktif maupun nonaktif dapat diedit.
4. Payload adalah full replacement untuk field form: `gameId`, `categoryId`, `name`, `isCustomPrice`, `priceAmount`, `estimateText`, dan `isActive` wajib dikirim. Penghilangan field bukan instruksi untuk mempertahankan nilai lama.
5. `gameId` dan `categoryId` harus merujuk game dan kategori yang ada serta belum soft-deleted. Layanan aktif hanya boleh menunjuk game dan kategori aktif, mengikuti ERD; aturan mengedit layanan menjadi nonaktif pada parent nonaktif tetap perlu dipastikan.
6. `name` wajib, di-trim, tidak kosong, dan maksimal 150 karakter. ERD belum menetapkan uniqueness scope; update tidak memberlakukan aturan nama unik baru tanpa keputusan product/API owner.
7. Harga fixed membutuhkan `isCustomPrice=false` dan `priceAmount >= 0`. Harga custom membutuhkan `isCustomPrice=true` dan `priceAmount=null`. Kombinasi yang tidak sesuai ditolak.
8. Mata uang tidak dikirim dalam request dan tidak disimpan per service pada proposal ERD. Usulan response publik menurunkan currency dari `defaultCurrency` platform; ownership, perubahan nilai, presisi, dan konsistensi dengan order snapshot perlu keputusan D-03/D-05.
9. `estimateText` wajib, di-trim, dan maksimal 100 karakter. Nilainya adalah teks estimasi, bukan SLA atau jaminan fulfillment.
10. `isActive` menentukan ketersediaan layanan baru pada storefront. Menonaktifkan layanan tidak menghapus order atau referensi historis.
11. Perubahan `gameId`, `categoryId`, nama, harga, atau estimasi hanya memengaruhi katalog untuk pemesanan baru. Snapshot pada order yang sudah dibuat tetap immutable sesuai Order ERD.
12. `updatedAt` ditetapkan service dalam UTC. `createdAt` tetap. Usulan: no-op tidak mengubah timestamp dan tidak menghasilkan audit perubahan.
13. Validasi parent dan update harus konsisten terhadap concurrent deactivation/deletion. MongoDB tidak menegakkan FK; mekanisme koordinasi/recheck write dan consistency guarantee masih harus diputuskan.
14. Kebutuhan serta event boundary audit untuk operasi update ini harus ditetapkan konsisten dengan BR-022/D-07. Field diff/allowlist, delivery/durability, atomicity, dan perilaku gagal belum ditetapkan (D-08); jangan mengasumsikan perubahan bisnis pasti bercommit bersama event.
15. Update tidak membuat atau menghapus game, kategori, order, payment, maupun data fulfillment.

## Request Headers

| Header         | E/N/T | Type   | M/O/C | Max Length | Description                                                                                                | Example                             |
| -------------- | ----- | ------ | ----- | ---------: | ---------------------------------------------------------------------------------------------------------- | ----------------------------------- |
| `Cookie`       | N     | String | M     |       4096 | Browser mengirim cookie access admin secara otomatis; token tidak dikirim melalui Authorization atau body. | `__Host-jokihub-admin-access={JWT}` |
| `Origin`       | N     | String | M     |        TBD | Origin dashboard harus cocok dengan allowlist.                                                             | `https://admin.example.test`        |
| `X-CSRF-Token` | N     | String | M     |        TBD | Token CSRF untuk request admin cookie-authenticated.                                                       | `{csrf-token}`                      |
| `Content-Type` | N     | String | M     |        100 | Format payload.                                                                                            | `application/json`                  |
| `Accept`       | N     | String | O     |        100 | Tipe media response yang diinginkan; default JSON.                                                         | `application/json`                  |
| `X-Request-Id` | N     | String | O     |        100 | ID korelasi untuk audit/support; jangan memuat PII.                                                        | `req-1f56a9`                        |
| `If-Match`     | N     | String | O     |        100 | ETag/version untuk optimistic concurrency bila platform mendukung; dukungan TBD.                           | `W/"v4"`                            |

## Request Parameters

| Parameter   | E/N/T | Type          | M/O/C | Max Length | Description                           | Example      |
| ----------- | ----- | ------------- | ----- | ---------: | ------------------------------------- | ------------ |
| `serviceId` | N     | String (path) | M     |         64 | ID immutable layanan yang diperbarui. | `svc_01JXYZ` |

## Request Body

| Attribute       | E/N/T | Type         | M/O/C | Max Length | Description                                           | Example               |
| --------------- | ----- | ------------ | ----- | ---------: | ----------------------------------------------------- | --------------------- |
| `gameId`        | N     | String       | M     |         64 | ID game yang memiliki layanan.                        | `gm_01JXYZ`           |
| `categoryId`    | N     | String       | M     |         64 | ID kategori layanan.                                  | `cat_01JXYZ`          |
| `name`          | N     | String       | M     |        150 | Nama display layanan.                                 | `Top Up 100 Diamonds` |
| `priceAmount`   | N     | Decimal/null | C     |       18,2 | Harga fixed >= 0 atau null untuk custom.              | `30000.00`            |
| `isCustomPrice` | N     | Boolean      | M     |          — | `true` untuk harga custom; `false` untuk harga fixed. | `false`               |
| `estimateText`  | N     | String       | M     |        100 | Estimasi display penyelesaian.                        | `Instan`              |
| `isActive`      | N     | Boolean      | M     |          — | Status publikasi layanan.                             | `true`                |

### Example Request Body

```json
{
  "gameId": "gm_01JXYZ",
  "categoryId": "cat_01JXYZ",
  "name": "Top Up 100 Diamonds",
  "priceAmount": 30000.0,
  "isCustomPrice": false,
  "estimateText": "Instan",
  "isActive": true
}
```

### Request Validation

1. Session cookie harus valid; `Origin` harus exact-allowlisted; token CSRF dan permission `service-update` harus valid. HTTP/service-code mapping auth dan CSRF tetap mengikuti API standard yang disetujui.
2. `serviceId` wajib, tidak kosong, maksimal 64 karakter, dan mengikuti format ID implementasi. Format invalid menghasilkan `SRVB02001`.
3. Body wajib JSON object valid. Tolak field tak dikenal termasuk `id`, `serviceId`, `createdAt`, `updatedAt`, dan `deletedAt` dengan `SRVB02001`.
4. Semua field bisnis wajib hadir. `gameId` dan `categoryId` berupa string non-kosong maksimal 64 karakter; `name` berupa string setelah trim, tidak kosong, maksimal 150 karakter.
5. `priceAmount` harus angka decimal atau `null` sesuai `isCustomPrice`; fixed price >= 0, custom price wajib null. Tolak string numerik, NaN, Infinity, nilai negatif, serta precision/scale lebih dari `18,2`.
6. `isCustomPrice` dan `isActive` wajib boolean JSON. Nilai string seperti `"true"` tidak diterima.
7. `estimateText` wajib string setelah trim, tidak kosong, dan maksimal 100 karakter.
8. `gameId` dan `categoryId` harus menunjuk record yang ada dan belum soft-deleted. Jika `isActive=true`, game dan kategori harus aktif. Referensi tidak valid/tidak eligible menghasilkan `SRVB02002`.
9. `serviceId` valid tetapi layanan tidak ditemukan atau soft-deleted menghasilkan `SRVB02003`.
10. Bila `If-Match` digunakan, mismatch terhadap versi terbaru tidak boleh menimpa perubahan concurrent; kode dan HTTP mapping konflik perlu diselaraskan dengan API standard.
11. Tolak query parameter yang tidak didefinisikan. Kegagalan database saat update mengembalikan HTTP 500 `SRVT03001` tanpa detail internal.

## Response Body

| Attribute            | E/N/T | Type               | M/O/C | Max Length | Description                           | Example                |
| -------------------- | ----- | ------------------ | ----- | ---------: | ------------------------------------- | ---------------------- |
| `code`               | N     | String             | M     |         20 | Service code.                         | `SRVS00000`            |
| `message`            | N     | String             | O     |        200 | Pesan hasil untuk consumer.           | `Service updated`      |
| `data`               | N     | Object/null        | M     |          — | Payload hasil; `null` ketika error.   | `{}`                   |
| `data.id`            | N     | String             | M     |         64 | ID layanan yang tetap.                | `svc_01JXYZ`           |
| `data.gameId`        | N     | String             | M     |         64 | ID game terbaru.                      | `gm_01JXYZ`            |
| `data.gameName`      | N     | String             | M     |        100 | Nama game hasil projection.           | `Mobile Legends`       |
| `data.categoryId`    | N     | String             | M     |         64 | ID kategori terbaru.                  | `cat_01JXYZ`           |
| `data.categoryName`  | N     | String             | M     |        100 | Nama kategori hasil projection.       | `Top Up`               |
| `data.name`          | N     | String             | M     |        150 | Nama layanan terbaru.                 | `Top Up 100 Diamonds`  |
| `data.priceAmount`   | N     | Decimal/null       | C     |       18,2 | Harga terbaru atau null untuk custom. | `30000.00`             |
| `data.isCustomPrice` | N     | Boolean            | M     |          — | Status custom pricing.                | `false`                |
| `data.estimateText`  | N     | String             | M     |        100 | Estimasi terbaru.                     | `Instan`               |
| `data.isActive`      | N     | Boolean            | M     |          — | Status aktif terbaru.                 | `true`                 |
| `data.createdAt`     | N     | String (date-time) | M     |         35 | Waktu pembuatan awal, tidak berubah.  | `2026-10-10T08:00:00Z` |
| `data.updatedAt`     | N     | String (date-time) | M     |         35 | Waktu update UTC.                     | `2026-10-11T10:20:00Z` |

### Success Example

```json
{
  "code": "SRVS00000",
  "message": "Service updated",
  "data": null
}
```

### Business Error Example

```json
{
  "code": "SRVB02002",
  "message": "Game or category is unavailable for this service",
  "data": null
}
```

### Technical Error Example

```json
{
  "code": "SRVT03001",
  "message": "Service catalog is temporarily unavailable",
  "data": null
}
```

## HTTP Status Codes

| HTTP Status | Usage                                                                                                                                 |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| `200`       | Layanan berhasil diperbarui sesuai baseline IFA project.                                                                              |
| `400`       | Identifier/payload/reference tidak valid atau akses admin ditolak sesuai baseline response project. Konfirmasi auth-specific mapping. |
| `500`       | Kegagalan service atau database yang tidak terduga.                                                                                   |

## Service Status Codes

Format: `[Service Prefix][Status Type][Category][Specific Error Code]`

| Code        | Type      | Category        | Meaning                                                           |
| ----------- | --------- | --------------- | ----------------------------------------------------------------- |
| `SRVS00000` | Success   | General         | Layanan berhasil diperbarui.                                      |
| `SRVB01001` | Business  | Authentication  | Administrator belum terautentikasi.                               |
| `SRVB01002` | Business  | Authentication  | Administrator tidak memiliki izin update layanan.                 |
| `SRVB02001` | Business  | User validation | Identifier atau payload tidak valid.                              |
| `SRVB02002` | Business  | User validation | Game/kategori tidak ditemukan, soft-deleted, atau tidak eligible. |
| `SRVB02003` | Business  | User validation | Layanan tidak ditemukan atau soft-deleted.                        |
| `SRVT03001` | Technical | Database        | Layanan gagal diperbarui atau database tidak tersedia.            |

## API Exposure Classification

| Classification            | Rationale                                                                                                                               |
| ------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| TBD (deployment exposure) | Operasi tulis katalog admin. Jika endpoint dapat diakses dari internet, klasifikasikan sebagai Public dan terapkan proteksi API publik. |

## Testing & Documentation Scope

| NFT / VA-Pentest handoff   | Value                                                                                                                                                                                  |
| -------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Service / module           | Game Service Management                                                                                                                                                                |
| Endpoint                   | `PUT /api/admin/services/v1/update/{serviceId}`                                                                                                                                        |
| Sample parameter / request | Path: `serviceId=svc_01JXYZ`; body uses `isCustomPrice=false`, `priceAmount=30000`                                                                                                     |
| Sample response            | Success code `SRVS00000`; use the complete Success Example below. Probe code `SRVB01002`; use its corresponding error example below. HTTP mapping remains subject to D-11 where noted. |
| NFT CPU / memory target    | Usulan: 2 vCPU / 4 GiB per instance; CPU ≤ 70%, memory ≤ 75%. Konfirmasi D-12 pending.                                                                                                 |
| NFT TPS target             | Usulan: 20 request/s pada 50 VUser. Forecast dan approval D-12 pending.                                                                                                                |

| Scope      | Required | Rationale / Handoff                                                                                                                                                                                                                                                                                                               |
| ---------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| NFT        | Yes      | Validasi target contract untuk route update admin yang sudah tercatat di inventory. Uji update setiap field, fixed/custom price, parent aktif/nonaktif/deleted, perubahan relasi, no-op, concurrency, serta persistence. Baseline VUser/CPU/memory/TPS pada tabel SLA merupakan usulan; forecast dan approval D-12 masih pending. |
| VA/Pentest | Yes      | Uji authentication/authorization, IDOR, mass assignment, validasi referensi, race condition, audit attribution, dan pastikan snapshot order lama tidak berubah.                                                                                                                                                                   |

## Assumptions

- Root envelope mengikuti Sprint 1; permission, payload, status code, dan prefix `SRV` mengikuti usulan pada [Create Service](create-service.md) dan masih menunggu persetujuan, walaupun method/path route tercatat pada inventory lokal.
- `PUT` dipakai karena modal edit mengirim seluruh field. Penggunaan `PATCH` memerlukan semantik omission/null yang berbeda.
- ERD belum menetapkan name uniqueness, currency schema, atau aturan update ke parent inactive; keputusan product/API owner diperlukan.
- Flag `isCustomPrice` merupakan field eksplisit pada proposed persistence model; UI saat ini merepresentasikannya sebagai `price === null`.
- BR-022 mewajibkan event, tetapi target audit store/service, delivery/atomicity, serta respons saat gagal belum diputuskan; diagram tidak mengasumsikan platform audit service tertentu.
- Optimistic concurrency/ETag, identity provider, role mapping, auth HTTP mapping, database engine, dan persetujuan target SLA masih TBD.

## References

- [Register keputusan terbuka Sprint 2](../../open-decisions.md) — definisi dan kriteria penutupan D-01–D-18.

- [Sprint 2 BRD](../../../BRD.md) — BR-019, pengelolaan layanan; BR-005, pembedaan harga tetap dan quote pada katalog publik.
- [Service ERD](../../../erd/service-erd.md) — field layanan, harga, relasi, lifecycle, dan order snapshot rule.
- [Sprint 1 Game ERD](../../../../sprint-1/erd/game-catalog-erd.md) — status dan lifecycle game.
- [Service Category ERD](../../../erd/service-category-erd.md) — status dan lifecycle kategori.
- [create-service.md](./create-service.md) — kontrak create dan validasi service baseline.
- [order-erd.md](../../../../sprint-3/solutioning/erd/order-erd.md) — snapshot transaksi/order yang tidak boleh berubah saat katalog diperbarui.
