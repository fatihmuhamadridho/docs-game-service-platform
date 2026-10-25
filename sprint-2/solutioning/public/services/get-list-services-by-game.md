# Service Catalog Service - Get List Services by Game Interface Agreement

> Current Go wire behavior for both registered paths: [catalog source audit](../catalog/source-audit-2026-10-23.md). Proposed target below remains separate.
> Open decisions: [catalog questions](../catalog/QUESTION.md).

> **Document Type:** API Contract  
> **Status:** Draft  
> **Service / Domain:** Service Catalog — Public Game Services  
> **Consumer / Partner:** JokiHub storefront checkout (`/order/:gameSlug`)

## Design

API ini mendokumentasikan target katalog layanan publik untuk route `/order/{gameSlug}`. Target menampilkan service aktif yang dapat dipilih, menyertakan kategori sebagai metadata pada setiap service, dan membedakan fixed price dari quote/custom price. **Implementasi Go saat ini** memiliki canonical `GET /api/games/v1/{gameSlug}/services` dan alias `GET /api/services/v1/games/{gameSlug}` dengan wire response `{id,name,category,price,customPrice,estimate}`; Decimal128 price dikonversi ke JSON float64. Canonical route menormalisasi slug dan menolak query, sedangkan alias tidak memvalidasi slug format maupun menolak query. Target schema di bawah belum wire-compatible dan tidak menetapkan kedua route ekuivalen.

## Sequence Diagram

```mermaid
sequenceDiagram
    actor User as Pengunjung
    participant Web as Checkout UI /order/{gameSlug}
    participant API as Public Service Catalog API
    participant DB as Game / Service / Category Database
    User->>Web: Buka /order/roblox
    Web->>API: GET /api/games/v1/{gameSlug}/services
    API->>API: Validasi dan normalisasi gameSlug
    API->>DB: Resolve game aktif berdasarkan slug
    alt Game tidak ditemukan/tidak aktif
        DB-->>API: Tidak ada game publik
API-->>Web: Source canonical returns 404 SVB02002; target HTTP mapping TBD
        Web-->>User: Tampilkan game tidak tersedia
    else Game aktif
        API->>DB: Ambil service aktif dan category aktif untuk game
        alt Services tersedia
            DB-->>API: Service list dan category metadata per service
            API-->>Web: 200 target {code,message,data[]} and root pagination
            Web-->>User: Tampilkan pilihan layanan dan harga
        else Tidak ada service tersedia
            DB-->>API: Daftar service kosong
            API-->>Web: 200 SVS00000, data [] and pagination.total=0
            Web-->>User: Tampilkan layanan belum tersedia
        else Database gagal
            DB-->>API: Kegagalan baca data
            API-->>Web: 500 SVT03001, data null
            Web-->>User: Tampilkan kegagalan dan opsi coba lagi
        end
    end
```

## Performance / SLA Targets

| Success Rate                                | VUser                                 | CPU / Memory                            | Response Time                                      |
| ------------------------------------------- | ------------------------------------- | --------------------------------------- | -------------------------------------------------- |
| TBD; confirm service criticality and target | TBD; confirm peak concurrent visitors | TBD; define after capacity estimate/NFT | TBD; response-time target remains TBD pending D-12 |

## General Information

| Component          | Value                                                                                                                                                      |
| ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Description        | Mengambil daftar layanan publik yang dapat dipesan berdasarkan game slug; item berada pada `data` dan metadata halaman pada `pagination` di root response. |
| Resource URL       | `https://{host}/api/games/v1/{gameSlug}/services`                                                                                                          |
| Transport Protocol | HTTPS, REST                                                                                                                                                |
| Request Verb       | GET                                                                                                                                                        |
| Content Type       | `application/json`                                                                                                                                         |
| Interaction Type   | Synchronous                                                                                                                                                |
| Security Policy    | Public read API; rate limiting, cache policy, dan input validation. Tidak mengembalikan data privat.                                                       |

### Path / Endpoint

| Section                  | Value                                   |
| ------------------------ | --------------------------------------- |
| Host / Domain            | `{host}` (environment-specific)         |
| Service Name             | Game / Service Catalog                  |
| Version                  | `v1`                                    |
| Resource                 | `/games/v1/{gameSlug}/services`         |
| Proposed target endpoint | `GET /api/games/v1/{gameSlug}/services` |

### API Versioning

- **Inventory status:** Canonical `GET /api/games/v1/{gameSlug}/services` tercatat pada source audit lokal; alias `GET /api/services/v1/games/{gameSlug}` juga tercatat dan memiliki validasi berbeda.
- **Target contract:** IFA mengusulkan contract pada canonical route yang sama; target payload, slug/query policy, eligibility, ordering, dan status belum diselaraskan dengan canonical maupun alias behavior.
- **Versioning:** Tetap `v1` pada usulan ini; perubahan wire behavior/alias memerlukan keputusan API owner.
- **Backward Compatibility:** Consumer belum dinilai. Field tambahan hanya dapat dianggap kompatibel setelah perilaku consumer diverifikasi; response target saat ini mengubah field wire sehingga memerlukan koordinasi consumer.
- **Reason:** Checkout membutuhkan katalog service yang terikat pada game route dan tervalidasi server-side.

## Business Rules

1. **Source behavior canonical:** `gameSlug` di-trim, case-folded, lalu divalidasi dengan slug regex; lookup mencakup game aktif. **Alias behavior:** path dipakai apa adanya, tanpa slug validation/query rejection. Pertahankan perbedaan ini sampai API owner menyetujui migrasi/deprecation.
2. **Target proposal:** hanya game/service/category eligible untuk order baru yang dikembalikan. Source audit membuktikan active game/service/category filters untuk canonical, tetapi tidak menyatakan seluruh `deleted_at` predicate target. Konfirmasi target lifecycle.
3. Service source saat ini dikelompokkan berdasarkan kategori aktif dan game aktif. Soft-delete/publishability target rules belum konsisten dengan bukti source dan ERD; keputusan perlu sebelum implementation claim.
4. Service hanya ditampilkan bila category-nya eligible pada target. Aturan kategori nonaktif tidak mengubah historical order snapshots.
5. Source service list saat ini disortir berdasarkan service name. Urutan target flat service list berdasarkan approved service display order belum didukung oleh schema `sort_order`; kategori juga punya current locale sort. Urutan target perlu keputusan.
6. Satu service terhadap satu game dan kategori mengikuti single-select UI saat ini, bukan aturan bisnis terkonfirmasi.
7. **Source wire fields:** `price` dapat null dan `customPrice` menunjukkan price custom/absent. **Target fields:** `priceAmount`, `currency`, `pricingType`, `isCustomPrice`, serta `estimateText`. Untuk target, `pricingType=FIXED` hanya bila `isCustomPrice=false` dan `priceAmount` berisi nominal; `QUOTE_REQUIRED` hanya bila `isCustomPrice=true` dan `priceAmount=null`. Currency per layanan tidak dimodelkan pada ERD; proposalnya diturunkan dari platform `defaultCurrency`, tetapi sumber, presisi, representasi wire, dan konsistensinya dengan Sprint 3 order snapshot perlu keputusan D-03/D-05. Nilai source Decimal128 menjadi float64; representasi exact masih open decision.
8. Target quote-required tidak boleh dikalkulasi/dibayar dengan harga rekaan (BR-005). UI saat ini memperlakukan custom service sebagai quote/contact, tetapi backend quote lifecycle berada di luar kontrak ini.
9. Source `estimate` adalah display field; target `estimateText` merupakan teks estimasi, bukan SLA/garansi penyelesaian.
10. Target menyediakan category/service identities; kategori tanpa eligible service tidak disertakan. Source current field shape berbeda sebagaimana dijelaskan di bawah.
11. **Source status:** canonical not-found/inactive returns HTTP 404 with `SVB02002`; alias returns 404 `SRVB02001`. Target HTTP mapping remains open in D-11.
12. Source routes have no pagination on service-by-game reads. Target proposes optional `page`/`limit` (defaults 1/20, maximum 100) to match its paged response; approve query policy under D-10 and compatibility/rollout under D-11. Data-volume evidence is still needed before treating the proposal as final.
13. Source implementation is read-only; an order flow must validate price and eligibility at submission in the Sprint 3 order contract.
14. Target `artworkKey` is not present in current game-by-slug source payload; its registry/mapping and field contract require decision.
15. Source handlers do not set a catalog cache policy. Cache, invalidation, TTL, and freshness are TBD.

## Request Headers

| Header            | E/N/T | Type   | M/O/C | Max Length | Description                                              | Example            |
| ----------------- | ----- | ------ | ----- | ---------: | -------------------------------------------------------- | ------------------ |
| `Accept`          | N     | String | O     |        100 | Response media type. Defaults to JSON.                   | `application/json` |
| `Accept-Language` | N     | String | O     |         35 | Preferred localized display copy; fallback behavior TBD. | `id-ID`            |

## Request Parameters

| Parameter  | E/N/T | Type   | M/O/C | Max Length | Description                                                   | Example  |
| ---------- | ----- | ------ | ----- | ---------: | ------------------------------------------------------------- | -------- |
| `gameSlug` | N     | String | M     |        100 | Slug game dari route order; URL-decode, trim, lalu lowercase. | `roblox` |

## Request Query Parameters

| Parameter | E/N/T | Type    | M/O/C | Max Length | Description                                              | Example |
| --------- | ----- | ------- | ----- | ---------: | -------------------------------------------------------- | ------- |
| `page`    | N     | Integer | O     |   9 digits | Target page number, one-based; default `1`, minimum `1`. | `1`     |
| `limit`   | N     | Integer | O     |   3 digits | Target page size; default `20`, allowed range `1`–`100`. | `20`    |

Query tersebut adalah target proposal. Canonical source saat ini menolak semua query; alias source memiliki acceptance berbeda.

## Request Body

None. GET request has no body.

### Request Validation

1. `gameSlug` wajib, tidak kosong, maksimal 100 karakter, dan mengikuti format slug `^[a-z0-9]+(?:-[a-z0-9]+)*$` setelah normalisasi. Nilai malformed mengembalikan `SVB02001`.
2. Slug harus resolve ke game aktif/publik. Game tidak ditemukan atau tidak tersedia mengembalikan `SVB02002`.
3. Target `page`, bila dikirim, harus integer >= 1; default `1`. Target `limit` harus integer 1–100; default `20`. Nilai invalid atau query parameter lain mengembalikan `SVB02001` pada target proposal.
4. **Source behavior:** canonical source saat ini menolak semua query, sedangkan alias menerima/mengabaikan query sesuai audit. Dukungan target `page`/`limit` dan unknown-query rejection memerlukan keputusan D-10/D-11.
5. Query harus membatasi service dan category ke record aktif/non-deleted/publik sebelum grouping.
6. Untuk fixed-price service, `priceAmount` harus tersedia dan non-negative. Untuk quote service, `priceAmount` harus null. Data catalog yang melanggar invariant tidak boleh dipresentasikan sebagai harga valid; catat data integrity error dan keluarkan/return safe service error sesuai kebijakan.
7. Hasil kosong untuk game aktif adalah sukses dengan `data: []` dan `pagination.total: 0`.
8. Query grouping tidak boleh menggandakan service karena join kategori atau asset.
9. Kegagalan database mengembalikan HTTP 500 `SVT03001` tanpa detail internal.

## Response Body

Sprint 2 target success responses use the Sprint 1 root-level `{code,message,data}` envelope. `data` is a flat array of eligible service objects and `pagination` is a root sibling. This list response does not add a separate root-level `game` field; each service carries its associated category metadata, while the requested `gameSlug` identifies the route. The current source does not paginate this route; target pagination is a proposed compatibility change tracked in D-10/D-11.

| Attribute               | E/N/T | Type         | M/O/C |    Max Length | Description                                                               | Example                        |
| ----------------------- | ----- | ------------ | ----- | ------------: | ------------------------------------------------------------------------- | ------------------------------ |
| `code`                  | N     | String       | M     |            20 | Service code.                                                             | `SVS00000`                     |
| `message`               | N     | String       | O     |           200 | Human-readable result.                                                    | `Game services retrieved`      |
| `data`                  | N     | Array        | M     | `limit` items | Flat page of eligible services; empty when no results.                    | `[]`                           |
| `data[].id`             | N     | String       | M     |            64 | Service ID.                                                               | `svc_01JXYZ`                   |
| `data[].name`           | N     | String       | M     |           150 | Public service name.                                                      | `Robux 400`                    |
| `data[].description`    | N     | String/null  | O     |           500 | Public detail/short description, if modeled.                              | `Top up Robux ke akun Roblox.` |
| `data[].category`       | N     | Object       | M     |             — | Category metadata associated with this service.                           | `{}`                           |
| `data[].category.id`    | N     | String       | M     |            64 | Category ID for grouping in the UI.                                       | `cat_01JXYZ`                   |
| `data[].category.name`  | N     | String       | M     |           100 | Category display name.                                                    | `Top Up Game`                  |
| `data[].category.slug`  | N     | String       | M     |           100 | Category URL/filter slug.                                                 | `top-up-game`                  |
| `data[].pricingType`    | N     | String       | M     |            20 | `FIXED` or `QUOTE_REQUIRED`.                                              | `FIXED`                        |
| `data[].priceAmount`    | N     | Decimal/null | C     |          18,2 | Fixed amount; null for quote-required service.                            | `25000.00`                     |
| `data[].currency`       | N     | String/null  | C     |             3 | ISO 4217 currency for fixed-price service; null for quote.                | `IDR`                          |
| `data[].estimateText`   | N     | String       | M     |           100 | Customer-facing estimate text.                                            | `Instan`                       |
| `data[].isCustomPrice`  | N     | Boolean      | M     |             — | Indicates quote/custom price.                                             | `false`                        |
| `pagination`            | N     | Object       | C     |             — | Root pagination metadata on successful list responses, sibling to `data`. | `{}`                           |
| `pagination.page`       | N     | Integer      | C     |      9 digits | Effective page, starting at 1.                                            | `1`                            |
| `pagination.limit`      | N     | Integer      | C     |      3 digits | Effective page size.                                                      | `20`                           |
| `pagination.totalPages` | N     | Integer      | C     |      9 digits | Number of pages; `0` when empty.                                          | `1`                            |
| `pagination.total`      | N     | Integer      | C     |     10 digits | Total eligible services before pagination.                                | `2`                            |

### Success Example

```json
{
  "code": "SVS00000",
  "message": "Game services retrieved",
  "data": [
    {
      "id": "svc_01JXYZ",
      "name": "Robux 400",
      "description": "Top up Robux ke akun Roblox.",
      "category": {
        "id": "cat_01JXYZ",
        "name": "Top Up Game",
        "slug": "top-up-game"
      },
      "pricingType": "FIXED",
      "priceAmount": 25000,
      "currency": "IDR",
      "estimateText": "Instan",
      "isCustomPrice": false
    },
    {
      "id": "svc_01JABC",
      "name": "Joki Star (Custom)",
      "description": null,
      "category": {
        "id": "cat_01JABC",
        "name": "Joki Game",
        "slug": "joki-game"
      },
      "pricingType": "QUOTE_REQUIRED",
      "priceAmount": null,
      "currency": null,
      "estimateText": "Hubungi admin untuk estimasi",
      "isCustomPrice": true
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
  "code": "SVB02002",
  "message": "Game is not available",
  "data": null
}
```

### Technical Error Example

```json
{
  "code": "SVT03001",
  "message": "Service catalog is temporarily unavailable",
  "data": null
}
```

## HTTP Status Codes

| HTTP Status | Usage                                                                                                                               |
| ----------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `200`       | Game aktif ditemukan; daftar layanan dapat kosong.                                                                                  |
| `400`       | Current canonical route returns `SVB02001` for malformed slug or any query; alias behavior differs and does not apply these checks. |
| `404`       | Current canonical route returns `SVB02002` and alias returns `SRVB02001` when the game is not found; target parity is TBD.          |
| `500`       | Unexpected service or catalog database failure.                                                                                     |

## Service Status Codes

Format: `[Service Prefix][Status Type][Category][Specific Error Code]`

| Code        | Type      | Category        | Meaning                                                                      |
| ----------- | --------- | --------------- | ---------------------------------------------------------------------------- |
| `SVS00000`  | Success   | General         | Daftar layanan untuk game berhasil dikembalikan.                             |
| `SVB02001`  | Business  | User validation | Game slug malformed atau parameter tidak valid.                              |
| `SVB02002`  | Business  | User validation | Game tidak ditemukan, tidak aktif, atau tidak dipublikasikan.                |
| `SVT03001`  | Technical | Database        | Game/service/category gagal dibaca atau database unavailable.                |
| `SRVS00000` | Success   | General         | Alias services mengembalikan daftar layanan dengan payload source yang sama. |
| `SRVB02001` | Business  | User validation | Game tidak ditemukan pada alias services.                                    |
| `SRVT03001` | Technical | Database        | Pembacaan katalog gagal pada alias services.                                 |

## API Exposure Classification

| Classification | Rationale                                                                           |
| -------------- | ----------------------------------------------------------------------------------- |
| Public         | Checkout storefront reads publicly available game/service catalog and pricing data. |

## Testing & Documentation Scope

| NFT / VA-Pentest handoff   | Value                                                                                                                                                                                |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Service / module           | Public Game Services                                                                                                                                                                 |
| Endpoint                   | `GET /api/games/v1/{gameSlug}/services`                                                                                                                                              |
| Sample parameter / request | Path: `gameSlug=roblox`; target query `page=1&limit=20`; canonical source currently rejects query                                                                                    |
| Sample response            | Success code `SVS00000`; use the complete Success Example below. Probe code `SVB02002`; use its corresponding error example below. HTTP mapping remains subject to D-11 where noted. |
| NFT CPU / memory target    | TBD; capacity evidence and approval required under D-12.                                                                                                                             |
| NFT TPS target             | TBD; traffic forecast and approval required under D-12.                                                                                                                              |

| Scope      | Required | Rationale / Handoff                                                                                                                                                                                   |
| ---------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| NFT        | Yes      | Uji popular game slug, flat service list and root pagination, cache hit/miss, banyak service per game, dan peak checkout load. TPS, resource, dan response-time targets TBD.                          |
| VA/Pentest | Yes      | Public pricing/catalog API. Uji slug validation, inactive game/service/category leakage, price integrity, quote-price handling, injection, enumeration, cache poisoning, dan excessive data exposure. |

## Assumptions

- Canonical and alias routes and source response envelope/status codes already exist. Proposed richer target response fields, normalization standardization, alias behavior, and HTTP mapping require API owner review.
- Route `/order/:gameSlug` currently uses static demo data. This contract replaces that data source; `/order/roblox` resolves slug from public `GAME_CATALOG`.
- [Service ERD](../../../erd/service-erd.md), [Game ERD](../../../../sprint-1/erd/game-catalog-erd.md), and [Service Category ERD](../../../erd/service-category-erd.md) define proposed catalog source fields and active-state rules.
- Service `description`, service/category ordering, currency storage, game artwork, public/published status, and category slug usage need confirmation; some response fields are derived/proposed for checkout UI.
- Current Checkout UI treats custom service as quote/contact and does not define backend quote workflow. Quote-required service must not enter priced checkout until that flow is designed.
- Pricing authority at order submission, inventory/availability race handling, tax/fees, authentication, cache invalidation, SLA, and HTTP error mapping are TBD or owned by order/payment contracts.
- Endpoint only reads service catalog for one game; it does not create orders, reserve stock, accept credentials, or process payment.

## References

- [Sprint 2 BRD](../../../BRD.md) — BR-004, daftar layanan untuk game; BR-005, pembedaan harga tetap dan quote.
- [game-catalog-erd.md](../../../../sprint-1/erd/game-catalog-erd.md) — public game slug and active state.
- [service-erd.md](../../../erd/service-erd.md) — service-game, price, estimate, and active fields.
- [service-category-erd.md](../../../erd/service-category-erd.md) — service category association and active behavior.
- [get-list-games.md](../../../../sprint-1/solutioning/public/games/get-list-games.md) — public catalog API conventions.

## Current Go Behavior vs Target

See [catalog source audit](../catalog/source-audit-2026-10-23.md) and [open decisions](../catalog/QUESTION.md). Canonical route: trimmed/case-folded slug validation, no query parameters, active game/service/category data, service-name ordering, current nested fields `{id,name,category:{id,name,slug},price,customPrice,estimate}`, and source-specific `SV` envelope/status mapping. Alias route: raw slug, unknown/malformed query accepted/ignored, same basic projection, `SRV` status prefix. Mongo Decimal128 is serialized as float64. The target IFA introduces fields absent from wire (currency, pricingType, description/artwork, estimates, totals), changes names, proposes different query/status/ordering and pricing semantics, and is not backward-compatible as written. Resolve D-03/D-10/D-11/D-14 and coordinate Sprint 3 checkout before changing implementation or calling the target final.
