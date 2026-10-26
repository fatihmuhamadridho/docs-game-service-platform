# Service Catalog Service - Get List Service Categories Interface Agreement

> **Document Type:** API Contract  
> **Status:** Draft  
> **Service / Domain:** Service Catalog — Service Categories  
> **Consumer / Partner:** JokiHub public storefront

> Catatan bukti: Perilaku implementasi dalam dokumen ini merangkum catatan route lokal terdahulu dan belum diverifikasi ulang terhadap kode pada pekerjaan ini. Perlakukan sebagai konteks historis; kontrak Sprint 2 adalah target proposal.

## Design

![Sprint 2 homepage design](../../../design/homepage.png)

![Sprint 2 game list design](../../../design/game-list.png)

## Sequence Diagram

```mermaid
sequenceDiagram
    actor User as Pengunjung
    participant Web as JokiHub storefront
    participant API as Service Category API
    participant DB as Category / Service Database
    User->>Web: Buka beranda atau katalog layanan
    Web->>API: GET /api/service-categories/v1/list
    API->>API: Catatan historis menyebut semua query ditolak; target policy mengikuti keputusan D-10/D-11
    alt Query tidak valid
        API-->>Web: Catatan historis: HTTP 400 / SCAB02001; target HTTP mapping TBD
        Web-->>User: Tampilkan error kategori
    else Query valid
        API->>DB: Ambil kategori aktif dengan service aktif yang publik
        alt Kategori tersedia
            DB-->>API: Daftar kategori + active service count
            API-->>Web: 200 SCAS00000, data[] dan pagination sibling di root; field target perlu keputusan
            Web-->>User: Tampilkan kategori layanan
        else Tidak ada kategori
            DB-->>API: Daftar kosong
            API-->>Web: 200 SCAS00000, data: [] dan pagination.total: 0
            Web-->>User: Tampilkan empty state atau sembunyikan section
        else Database gagal
            DB-->>API: Kegagalan query
            API-->>Web: 500 SCAT03001, data null
            Web-->>User: Tampilkan kegagalan dan opsi coba lagi
        end
    end
```

## Performance / SLA Targets

> Status usulan: angka baseline turunan Sprint 1; belum menjadi komitmen Sprint 2. Forecast, kondisi ukur, dan persetujuan owner masih diperlukan (D-12).

| Success Rate                                                  | VUser                              | CPU / Memory                                                                | Response Time                |
| ------------------------------------------------------------- | ---------------------------------- | --------------------------------------------------------------------------- | ---------------------------- |
| 99.9% monthly availability; valid-request server error ≤ 0.1% | 50 VUser / 20 request/s NFT target | Initial 2 vCPU / 4 GiB per instance; CPU ≤ 70%, memory ≤ 75% at target load | Read p95 ≤ 500 ms; p99 ≤ 2 s |

## General Information

| Component          | Value                                                                                                |
| ------------------ | ---------------------------------------------------------------------------------------------------- |
| Description        | Mengambil kategori layanan publik yang aktif dan memiliki layanan aktif.                             |
| Resource URL       | `https://{host}/api/service-categories/v1/list`                                                      |
| Transport Protocol | HTTPS, REST                                                                                          |
| Request Verb       | GET                                                                                                  |
| Content Type       | `application/json`                                                                                   |
| Interaction Type   | Synchronous                                                                                          |
| Security Policy    | Public read API; rate limiting, cache policy, dan input validation. Tidak mengembalikan data privat. |

### Path / Endpoint

| Section                  | Value                                 |
| ------------------------ | ------------------------------------- |
| Host / Domain            | `{host}` (environment-specific)       |
| Service Name             | Service Catalog                       |
| Version                  | `v1`                                  |
| Resource                 | `/service-categories/v1/list`         |
| Proposed target endpoint | `GET /api/service-categories/v1/list` |

### API Versioning

- **Inventory status:** Catatan route lokal terdahulu mencatat `GET /api/service-categories/v1/list`; detail tersebut belum diverifikasi ulang terhadap kode pada pekerjaan ini.
- **Target contract:** IFA mengusulkan perubahan/pelengkapan perilaku pada route yang sama; query, response fields tambahan, eligibility, dan target semantics belum disetujui terhadap source behavior.
- **Versioning:** Tetap `v1` pada usulan ini; migrasi wire behavior memerlukan keputusan API owner.
- **Backward Compatibility:** Consumer belum dinilai. Field tambahan hanya dapat dianggap kompatibel setelah perilaku consumer diverifikasi; perubahan slug, field wajib, atau semantik perlu koordinasi dan persetujuan consumer.
- **Reason:** Storefront membutuhkan kategori dari catalog source of truth, bukan daftar statis UI.

## Business Rules

1. **Konteks route historis:** Catatan lokal terdahulu menyebut kategori aktif dengan setidaknya satu layanan aktif yang terkait dengan game aktif dan kategori kosong disembunyikan. Target `deleted_at`/publishability predicate belum disetujui.
2. Target IFA mengusulkan category/service `deleted_at IS NULL` dan aturan publishability storefront. Catatan route historis tidak menetapkan predicate deleted khusus kategori/service; jangan menganggap target sudah berjalan.
3. **Konteks route historis:** Catatan lokal terdahulu menyebut `serviceCount` sebagai jumlah layanan aktif yang tertaut pada game aktif untuk kategori. Product harus menentukan apakah metric publik dan admin sama atau berbeda serta mendefinisikan predicate melalui D-14.
4. **Konteks route historis:** Catatan lokal terdahulu menyebut urutan locale Indonesia. Target mengusulkan display order lalu `name ASC` dan category ID, tetapi `sort_order` belum disetujui di ERD. Product/catalog owner harus memilih aturan/source urutan.
5. Catatan route terdahulu menyebut field `id`, `name`, `slug`, dan `description`. Target slug stability dan mutability memerlukan policy, termasuk redirect bila dipakai sebagai URL.
6. `iconKey`/`sortOrder` hanya muncul pada target response proposal. `iconKey` harus berasal dari asset allowlist jika disetujui; jangan anggap kedua field sudah tersedia pada implementasi.
7. Kategori dan layanan yang tidak aktif, terhapus, belum dipublikasikan, atau hanya untuk admin tidak boleh bocor ke response publik.
8. **Target proposal:** kategori dikembalikan per halaman dengan `data` sebagai array dan `pagination` sebagai sibling root (`page`, `limit`, `total`, `totalPages`). Catatan route historis menyebut query ditolak dan pagination tidak tersedia; setujui query policy pada D-10 serta rollout/kompatibilitas pada D-11.
9. Jika kategori aktif tidak punya layanan aktif, kategori disembunyikan dari storefront untuk menghindari navigasi kosong.
10. Description dan display name diperlakukan sebagai teks biasa; frontend harus melakukan escaping. Tidak mengembalikan markup HTML aktif.
11. Response kosong merupakan sukses dengan `data: []`.
12. Catatan route terdahulu tidak menetapkan cache policy. Cache/TTL/freshness dan invalidasi setelah mutasi admin semuanya TBD; jangan nyatakan kategori sedang di-cache.

## Request Headers

| Header            | E/N/T | Type   | M/O/C | Max Length | Description                                               | Example            |
| ----------------- | ----- | ------ | ----- | ---------: | --------------------------------------------------------- | ------------------ |
| `Accept`          | N     | String | O     |        100 | Response media type. Defaults to JSON.                    | `application/json` |
| `Accept-Language` | N     | String | O     |         35 | Bahasa display copy; kategori saat ini diasumsikan id-ID. | `id-ID`            |

## Request Parameters

None. Endpoint tidak menggunakan path parameter.

## Request Query Parameters

| Parameter | E/N/T | Type    | M/O/C | Max Length | Description                                              | Example |
| --------- | ----- | ------- | ----- | ---------: | -------------------------------------------------------- | ------- |
| `page`    | N     | Integer | O     |   9 digits | Target page number, one-based; default `1`, minimum `1`. | `1`     |
| `limit`   | N     | Integer | O     |   3 digits | Target page size; default `20`, allowed range `1`–`100`. | `20`    |

## Request Body

None. GET request has no body.

### Request Validation

1. Target `page`, bila dikirim, harus integer >= 1; default `1`.
2. Target `limit`, bila dikirim, harus integer 1–100; default `20`.
3. Query parameter yang tidak dikenal ditolak dengan `SCAB02001` pada target proposal.
4. **Konteks route historis:** Catatan lokal terdahulu menyebut route menolak query dengan HTTP 400 `SCAB02001`; dukungan target `page`/`limit` memerlukan keputusan D-10/D-11.
5. Database query harus membatasi kategori dan service pada record aktif/non-deleted/public sebelum menghitung `serviceCount`.
6. Aggregate count tidak boleh menggandakan service akibat join tambahan; hitung distinct service ID.
7. Kegagalan database mengembalikan HTTP 500 `SCAT03001` tanpa detail internal.

## Response Body

| Attribute               | E/N/T | Type        | M/O/C |                                  Max Length | Description                                                                      | Example                        |
| ----------------------- | ----- | ----------- | ----- | ------------------------------------------: | -------------------------------------------------------------------------------- | ------------------------------ |
| `code`                  | N     | String      | M     |                                          20 | Service code.                                                                    | `SCAS00000`                    |
| `message`               | N     | String      | O     |                                         200 | Human-readable result.                                                           | `Service categories retrieved` |
| `data`                  | N     | Array/null  | M     | `pagination.limit` items (max 100 proposed) | Public category page; empty when no category has active service; null on errors. | `[]`                           |
| `pagination`            | N     | Object      | C     |                                           — | Root pagination metadata on successful list responses.                           | `{}`                           |
| `pagination.page`       | N     | Integer     | C     |                                    9 digits | Effective page, default `1`, starting at 1.                                      | `1`                            |
| `pagination.limit`      | N     | Integer     | C     |                                    3 digits | Effective page size; default `20`, maximum proposed `100`.                       | `20`                           |
| `pagination.total`      | N     | Integer     | C     |                                   10 digits | Total categories before pagination.                                              | `2`                            |
| `pagination.totalPages` | N     | Integer     | C     |                                    9 digits | Number of pages; `0` when empty.                                                 | `1`                            |
| `data[].id`             | N     | String      | M     |                                          64 | Stable category identifier.                                                      | `cat_01JXYZ`                   |
| `data[].name`           | N     | String      | M     |                                         100 | Display name.                                                                    | `Top Up Game`                  |
| `data[].slug`           | N     | String      | M     |                                         100 | URL-safe category identifier.                                                    | `top-up-game`                  |
| `data[].description`    | N     | String/null | O     |                                         500 | Public category description.                                                     | `Isi ulang game favoritmu.`    |
| `data[].iconKey`        | N     | String/null | O     |                                         160 | Approved icon asset key, jika dimodelkan/dikelola katalog.                       | `top-up`                       |
| `data[].serviceCount`   | N     | Integer     | M     |                                   10 digits | Jumlah layanan aktif dan publik pada kategori.                                   | `18`                           |
| `data[].sortOrder`      | N     | Integer     | O     |                                   10 digits | Urutan display; field/source perlu ditetapkan.                                   | `1`                            |

### Success Example

```json
{
  "code": "SCAS00000",
  "message": "Service categories retrieved",
  "data": [
    {
      "id": "cat_01JXYZ",
      "name": "Top Up Game",
      "slug": "top-up-game",
      "description": "Isi ulang game favoritmu.",
      "iconKey": "top-up",
      "serviceCount": 18,
      "sortOrder": 1
    },
    {
      "id": "cat_01JABC",
      "name": "Joki Game",
      "slug": "joki-game",
      "description": "Layanan joki game pilihan.",
      "iconKey": "joki",
      "serviceCount": 15,
      "sortOrder": 2
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 2,
    "totalPages": 1
  }
}
```

### Business Error Example

```json
{
  "code": "SCAB02001",
  "message": "Invalid category-list parameters",
  "data": null
}
```

### Technical Error Example

```json
{
  "code": "SCAT03001",
  "message": "Service catalog is temporarily unavailable",
  "data": null
}
```

## HTTP Status Codes

| HTTP Status | Usage                                                                                                                                                                            |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `200`       | Request valid; category list may be empty.                                                                                                                                       |
| `400`       | Earlier route notes describe rejection of query parameters with `SCAB02001`; this was not reverified against code in this work. Target query policy remains subject to approval. |
| `500`       | Unexpected service or catalog database failure.                                                                                                                                  |

## Service Status Codes

Format: `[Service Prefix][Status Type][Category][Specific Error Code]`

| Code        | Type      | Category        | Meaning                                                               |
| ----------- | --------- | --------------- | --------------------------------------------------------------------- |
| `SCAS00000` | Success   | General         | Daftar kategori layanan berhasil dikembalikan.                        |
| `SCAB02001` | Business  | User validation | Query parameter tidak didukung/tidak valid.                           |
| `SCAT03001` | Technical | Database        | Kategori atau service availability gagal dibaca/database unavailable. |

## API Exposure Classification

| Classification | Rationale                                                                                                 |
| -------------- | --------------------------------------------------------------------------------------------------------- |
| Public         | Storefront membaca metadata kategori dan ketersediaan layanan yang dipublikasikan; tidak ada data privat. |

## Testing & Documentation Scope

| NFT / VA-Pentest handoff   | Value                                                                                                                                                                                  |
| -------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Service / module           | Public Service Categories                                                                                                                                                              |
| Endpoint                   | `GET /api/service-categories/v1/list`                                                                                                                                                  |
| Sample parameter / request | Target proposal: `page=1&limit=20`; earlier route notes describe rejection of query parameters                                                                                         |
| Sample response            | Success code `SCAS00000`; use the complete Success Example below. Probe code `SCAB02001`; use its corresponding error example below. HTTP mapping remains subject to D-11 where noted. |
| NFT CPU / memory target    | Usulan: 2 vCPU / 4 GiB per instance; CPU ≤ 70%, memory ≤ 75%. Konfirmasi D-12 pending.                                                                                                 |
| NFT TPS target             | Usulan: 20 request/s pada 50 VUser. Forecast dan approval D-12 pending.                                                                                                                |

| Scope      | Required | Rationale / Handoff                                                                                                                                                   |
| ---------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| NFT        | Yes      | Uji latency query category-service aggregate, cache hit/miss, kategori tanpa layanan, dan peak storefront traffic. TPS dan resource target menunggu traffic forecast. |
| VA/Pentest | Yes      | Public API. Uji inactive/deleted category leakage, inactive service count leakage, injection, asset reference handling, caching, dan excessive data exposure.         |

## Assumptions

- Catatan route lokal terdahulu menyebut route `/api/service-categories/v1/list`, envelope root, prefix `SCA`, source fields, dan HTTP behavior. Perilaku kode belum diverifikasi ulang pada pekerjaan ini. Target response mengikuti `{code,message,data}`; perubahan field/query/cache semantics dan HTTP mapping masih memerlukan persetujuan.
- [Service Category ERD](../../../erd/service-category-erd.md) dan [Service ERD](../../../erd/service-erd.md) menjadi sumber kategori dan relasi service. `is_active` serta `deleted_at` mengatur kelayakan storefront.
- Kategori UI di `HomeV2` berupa static presentational data dengan title/detail/icon, bukan bukti bahwa data kategori atau klaim ketersediaan telah dikonfirmasi.
- Category sort order, icon asset key, localization, service publishability, category count semantics, cache invalidation/TTL, rate limits, dan traffic targets masih TBD.
- Listing detail service/game, pencarian kategori, admin CRUD, dan category analytics berada di luar scope endpoint ini. Dukungan target query `page`/`limit` dan kompatibilitas dengan source menunggu D-10/D-11.

## References

- [Sprint 2 decision register](../../open-decisions.md) — D-02/D-09/D-10/D-11/D-12/D-14/D-15.
- [Sprint 2 BRD](../../../BRD.md) — BR-003, penemuan kategori publik dan pencegahan pilihan pembelian kosong.
- [service-category-erd.md](../../../erd/service-category-erd.md) — category attributes, active/deleted behavior, and service count notes.
- [service-erd.md](../../../erd/service-erd.md) — active service and category relationship.
- [get-list-games.md](../../../../sprint-1/solutioning/public/games/get-list-games.md) — public catalog endpoint conventions.
