# Admin Dashboard - Create Service Category Interface Agreement

> **Document Type:** IFA / API Contract  
> **Status:** Draft  
> **Service / Domain:** Admin Dashboard — Service Category Management  
> **Consumer / Partner:** JokiHub Admin Dashboard (authorized administrator)

## Design

![Sprint 2 dashboard design](../../../design/dashboard.png)

## Sequence Diagram

```mermaid
sequenceDiagram
    actor Admin as Administrator
    participant UI as Admin Dashboard UI
    participant API as Service Category Management API
    participant DB as Catalog Database
    participant Audit as Audit policy (TBD)
    Admin->>UI: Isi form kategori baru dan simpan
    UI->>API: POST /api/admin/service-categories/v1/create dengan payload dan kredensial admin
    API->>API: Autentikasi dan otorisasi izin service-category-create
    alt Tidak terautentikasi atau tidak berizin
        API-->>UI: 400 SCAB01001 / SCAB01002, data null
        UI-->>Admin: Tampilkan pesan akses
    else Berizin
        API->>API: Validasi dan normalisasi name/slug/description
        alt Payload tidak valid
            API-->>UI: 400 SCAB02001, data null
            UI-->>Admin: Tampilkan kesalahan field
        else Payload valid
            API->>DB: Cek keunikan nama dan slug
            alt Nama atau slug duplikat
                DB-->>API: Unique conflict
                API-->>UI: 400 SCAB02002, data null
                UI-->>Admin: Tampilkan konflik nama atau slug
            else Tidak duplikat
                API->>DB: Simpan kategori
                alt Penyimpanan berhasil
                    DB-->>API: Kategori baru dan ID
                    Note over API,Audit: Jalur sukses 200 hanya berlaku setelah kebijakan D-07/D-08 disetujui dan outcome audit memenuhi kebijakan; detail delivery tidak ditetapkan di sini
                    API-->>UI: 200 SCAS00000, data (bersyarat pada kebijakan audit yang disetujui)
                    UI-->>Admin: Tampilkan konfirmasi dan kategori baru setelah outcome sukses terkonfirmasi
                else Database gagal
                    DB-->>API: Kegagalan penyimpanan
                    API-->>UI: 500 SCAT03001, data null
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

| Component          | Value                                                                                                                                               |
| ------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| Description        | Membuat record kategori layanan baru untuk katalog JokiHub.                                                                                         |
| Resource URL       | `https://{host}/api/admin/service-categories/v1/create`                                                                                             |
| Transport Protocol | HTTPS, REST                                                                                                                                         |
| Request Verb       | POST                                                                                                                                                |
| Content Type       | `application/json`                                                                                                                                  |
| Interaction Type   | Synchronous                                                                                                                                         |
| Security Policy    | Admin authentication dan izin `service-category-create`. Identity provider dan permission final TBD. Terapkan rate limiting dan audit actor/action. |

### Path / Endpoint

| Section                  | Value                                          |
| ------------------------ | ---------------------------------------------- |
| Host / Domain            | `{host}` (environment-specific)                |
| Service Name             | Service Category Management                    |
| Version                  | `v1`                                           |
| Resource                 | `/admin/service-categories/v1/create`          |
| Proposed target endpoint | `POST /api/admin/service-categories/v1/create` |

### API Versioning

- **Inventory status:** Method/path yang sama tercatat pada route inventory repo ini; bagian ini bukan klaim bahwa route belum ada.
- **Target contract:** IFA mengusulkan perilaku pada `POST /api/admin/service-categories/v1/create`; payload, permission, response, dan status target belum disetujui terhadap inventory.
- **Versioning:** Tetap `v1` pada usulan ini; perubahan kompatibilitas perlu keputusan API owner.
- **Backward Compatibility:** Consumer belum dinilai. Penambahan field hanya dapat dianggap kompatibel setelah perilaku consumer diverifikasi; perubahan field/semantik/status yang mematahkan consumer memerlukan keputusan versioning API owner.
- **Reason:** Admin Dashboard memerlukan operasi untuk menambah kelompok layanan ke katalog.

## Business Rules

1. Hanya administrator terautentikasi dengan izin `service-category-create` yang dapat membuat kategori. Role mapping final TBD.
2. `categoryId`, `createdAt`, dan `updatedAt` dibuat oleh service; client tidak boleh mengirim nilai tersebut.
3. `name` dan `slug` wajib. Nama disimpan setelah trim; slug disimpan lowercase setelah trim dan harus sesuai format URL-safe.
4. Usulan target: nama kategori ternormalisasi unik case-insensitive dan slug unik. Cakupan record yang diperiksa, aturan reuse setelah soft delete, dan mekanisme penegakan terhadap penulisan bersamaan belum disetujui; rancangan indeks pada ERD masih kandidat.
5. Kategori duplikat tidak menimpa atau menggabungkan record yang sudah ada. Kembalikan konflik validasi yang sama untuk duplicate name atau slug agar tidak mengekspos record lain.
6. `description` opsional; nilai kosong setelah trim dinormalisasi menjadi `null`. Batas 500 karakter mengikuti proposal ERD.
7. `isActive` menentukan apakah kategori tersedia untuk assignment/publikasi katalog. Default `true` mengikuti proposal ERD bila field tidak dikirim; konfirmasi default sebelum implementasi.
8. Kategori dibuat dengan `serviceCount=0`. Pembuatan kategori tidak membuat layanan dan tidak mengubah layanan yang sudah ada.
9. `createdAt` dan `updatedAt` diset ke waktu server UTC yang sama pada pembuatan.
10. BR-022 menetapkan kebutuhan audit administratif minimum, tetapi event boundary (attempt/success/failure), payload, dan perilaku target create ini harus diselaraskan pada D-07. Store, delivery, atomicity, retry/deduplication, dan respons kegagalan masih menunggu D-08; jangan menyiratkan event pasti berhasil dicatat.
11. Endpoint tidak menerima `deletedAt`; kategori baru selalu non-deleted.
12. Retry setelah timeout dapat mengulangi create. Idempotency key dan perilaku retry belum ditetapkan; putuskan kebutuhan tersebut sebelum implementasi. Keunikan nama/slug adalah aturan bisnis terpisah dan tidak dengan sendirinya menjamin idempotensi request.

## Request Headers

| Header         | E/N/T | Type   | M/O/C | Max Length | Description                                                                                                | Example                             |
| -------------- | ----- | ------ | ----- | ---------: | ---------------------------------------------------------------------------------------------------------- | ----------------------------------- |
| `Cookie`       | N     | String | M     |       4096 | Browser mengirim cookie access admin secara otomatis; token tidak dikirim melalui Authorization atau body. | `__Host-jokihub-admin-access={JWT}` |
| `Origin`       | N     | String | M     |        TBD | Origin dashboard harus cocok dengan allowlist.                                                             | `https://admin.example.test`        |
| `X-CSRF-Token` | N     | String | M     |        TBD | Token CSRF untuk request admin cookie-authenticated.                                                       | `{csrf-token}`                      |
| `Content-Type` | N     | String | M     |        100 | Format payload.                                                                                            | `application/json`                  |
| `Accept`       | N     | String | O     |        100 | Tipe media response yang diinginkan; default JSON.                                                         | `application/json`                  |
| `X-Request-Id` | N     | String | O     |        100 | ID korelasi untuk audit/support; jangan memuat PII.                                                        | `req-3c2f10`                        |

## Request Parameters

Tidak ada path/query parameter.

## Request Body

| Attribute     | E/N/T | Type        | M/O/C | Max Length | Description                                                     | Example                        |
| ------------- | ----- | ----------- | ----- | ---------: | --------------------------------------------------------------- | ------------------------------ |
| `name`        | N     | String      | M     |        100 | Nama tampilan kategori.                                         | `Top Up`                       |
| `slug`        | N     | String      | M     |        100 | Identifier URL-safe, lowercase dan dipisahkan tanda hubung.     | `top-up`                       |
| `description` | N     | String/null | O     |        500 | Deskripsi kategori; whitespace-only dinormalisasi menjadi null. | `Layanan pengisian saldo game` |
| `isActive`    | N     | Boolean     | O     |          — | Status aktif; default usulan `true`.                            | `true`                         |

### Example Request Body

```json
{
  "name": "Top Up",
  "slug": "top-up",
  "description": "Layanan pengisian saldo game",
  "isActive": true
}
```

### Request Validation

1. Session cookie harus valid; `Origin` harus exact-allowlisted; token CSRF dan permission `service-category-create` harus valid. HTTP/service-code mapping auth dan CSRF tetap mengikuti API standard yang disetujui.
2. Body wajib berupa JSON object valid. Tolak field yang tidak dikenal, termasuk `id`, `categoryId`, `createdAt`, `updatedAt`, `deletedAt`, dan `serviceCount`, dengan `SCAB02001`.
3. `name` wajib berupa string; trim sebelum validasi dan simpan. Nilai setelah trim tidak boleh kosong dan panjangnya maksimal 100 karakter.
4. `slug` wajib berupa string; trim dan ubah ke lowercase sebelum validasi. Nilai harus cocok dengan `^[a-z0-9]+(?:-[a-z0-9]+)*$` dan panjang maksimal 100 karakter. UI dapat mengisi slug dari nama, tetapi API tetap menerima slug eksplisit.
5. `description`, bila dikirim, harus string atau `null`; trim sebelum simpan, maksimal 500 karakter. Nilai kosong/whitespace menjadi `null`.
6. `isActive`, bila dikirim, harus boolean JSON. Jika tidak dikirim, gunakan default `true` yang masih menunggu persetujuan product/API owner.
7. Bila aturan uniqueness nama/slug disetujui, konflik menghasilkan `SCAB02002`. Cakupan uniqueness dan mekanisme penyimpanan untuk mencegah race masih TBD; jangan menganggap kandidat indeks ERD sebagai constraint yang sudah dipilih.
8. Jika kategori dengan slug sama pernah soft-deleted, aturan penggunaan ulang slug belum diputuskan; perlakukan sebagai konflik sampai aturan reuse disetujui.
9. Kegagalan database saat penyimpanan menghasilkan HTTP 500 `SCAT03001`; jangan mengembalikan detail query atau constraint internal.

## Response Body

| Attribute           | E/N/T | Type               | M/O/C | Max Length | Description                                        | Example                        |
| ------------------- | ----- | ------------------ | ----- | ---------: | -------------------------------------------------- | ------------------------------ |
| `code`              | N     | String             | M     |         20 | Service code.                                      | `SCAS00000`                    |
| `message`           | N     | String             | O     |        200 | Pesan hasil untuk consumer.                        | `Service category created`     |
| `data`              | N     | Object/null        | M     |          — | Payload hasil; `null` ketika error.                | `{}`                           |
| `data.id`           | N     | String             | M     |         64 | ID immutable yang dibuat service.                  | `cat_01JXYZ`                   |
| `data.name`         | N     | String             | M     |        100 | Nama kategori setelah trim.                        | `Top Up`                       |
| `data.slug`         | N     | String             | M     |        100 | Slug kategori normalized.                          | `top-up`                       |
| `data.description`  | N     | String/null        | O     |        500 | Deskripsi kategori.                                | `Layanan pengisian saldo game` |
| `data.isActive`     | N     | Boolean            | M     |          — | Status aktif tersimpan.                            | `true`                         |
| `data.serviceCount` | N     | Integer            | M     |  10 digits | Jumlah layanan saat dibuat; selalu `0`.            | `0`                            |
| `data.createdAt`    | N     | String (date-time) | M     |         35 | Waktu pembuatan UTC.                               | `2026-10-11T09:00:00Z`         |
| `data.updatedAt`    | N     | String (date-time) | M     |         35 | Waktu update; sama dengan `createdAt` saat create. | `2026-10-11T09:00:00Z`         |

### Success Example

```json
{
  "code": "SCAS00000",
  "message": "Service category created",
  "data": {
    "id": "cat_01JXYZ",
    "name": "Top Up",
    "slug": "top-up",
    "description": "Layanan pengisian saldo game",
    "isActive": true,
    "serviceCount": 0,
    "createdAt": "2026-10-11T09:00:00Z",
    "updatedAt": "2026-10-11T09:00:00Z"
  }
}
```

### Business Error Example

```json
{
  "code": "SCAB02002",
  "message": "Service category name or slug already exists",
  "data": null
}
```

### Technical Error Example

```json
{
  "code": "SCAT03001",
  "message": "Service category data is temporarily unavailable",
  "data": null
}
```

## HTTP Status Codes

| HTTP Status | Usage                                                                                                                                      |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| `200`       | Kategori berhasil dibuat sesuai baseline IFA project; konfirmasi apakah standar API menetapkan `201`.                                      |
| `400`       | Payload tidak valid, akses admin ditolak sesuai baseline response project, atau nama/slug duplikat. Konfirmasi auth-specific HTTP mapping. |
| `500`       | Kegagalan service atau database yang tidak terduga.                                                                                        |

## Service Status Codes

Format: `[Service Prefix][Status Type][Category][Specific Error Code]`

| Code        | Type      | Category        | Meaning                                                      |
| ----------- | --------- | --------------- | ------------------------------------------------------------ |
| `SCAS00000` | Success   | General         | Kategori layanan berhasil dibuat.                            |
| `SCAB01001` | Business  | Authentication  | Administrator belum terautentikasi.                          |
| `SCAB01002` | Business  | Authentication  | Administrator tidak memiliki izin create kategori.           |
| `SCAB02001` | Business  | User validation | Field wajib, format, panjang, atau tipe payload tidak valid. |
| `SCAB02002` | Business  | User validation | Nama atau slug kategori sudah digunakan.                     |
| `SCAT03001` | Technical | Database        | Kategori gagal disimpan atau database tidak tersedia.        |

## API Exposure Classification

| Classification            | Rationale                                                                                                                                     |
| ------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| TBD (deployment exposure) | Operasi tulis admin untuk katalog. Jika endpoint dapat diakses dari internet, klasifikasikan sebagai Public dan terapkan proteksi API publik. |

## Testing & Documentation Scope

| NFT / VA-Pentest handoff   | Value                                                                                                                                                                                  |
| -------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Service / module           | Service Category Management                                                                                                                                                            |
| Endpoint                   | `POST /api/admin/service-categories/v1/create`                                                                                                                                         |
| Sample parameter / request | JSON body: `name="Top Up"`, `slug="top-up"`, `isActive=true`                                                                                                                           |
| Sample response            | Success code `SCAS00000`; use the complete Success Example below. Probe code `SCAB01002`; use its corresponding error example below. HTTP mapping remains subject to D-11 where noted. |
| NFT CPU / memory target    | Usulan: 2 vCPU / 4 GiB per instance; CPU ≤ 70%, memory ≤ 75%. Konfirmasi D-12 pending.                                                                                                 |
| NFT TPS target             | Usulan: 20 request/s pada 50 VUser. Forecast dan approval D-12 pending.                                                                                                                |

| Scope      | Required | Rationale / Handoff                                                                                                                                                                                                                                                                                                   |
| ---------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| NFT        | Yes      | Validasi target contract untuk route create admin yang sudah tercatat di inventory. Uji payload valid, batas panjang, duplikasi, request bersamaan dengan slug sama, persistence, dan retry setelah timeout. Baseline VUser/CPU/memory/TPS pada tabel SLA merupakan usulan; forecast dan approval D-12 masih pending. |
| VA/Pentest | Yes      | Verifikasi authentication/authorization, privilege boundaries, mass assignment, input validation, injection, audit attribution, dan tidak ada overwrite record duplikat.                                                                                                                                              |

## Assumptions

- Target contract untuk response envelope, service prefix/codes, dan permission belum disetujui; method/path route tercatat pada inventory lokal.
- Normalisasi dan uniqueness nama/slug, batas field, serta default `isActive=true` adalah proposal; owner perlu menyetujui aturan dan mekanisme penegakannya sebelum implementasi.
- Slug reuse setelah soft delete belum ditetapkan. Kontrak menganggap slug tetap konflik sampai keputusan final.
- BR-022 mewajibkan event untuk aksi administratif ini, tetapi target audit store/service, bentuk event, delivery/atomicity, serta respons saat gagal belum diputuskan; diagram tidak mengasumsikan platform audit service tertentu.
- Idempotency key belum menjadi bagian kontrak. Jika client retry harus menjamin exactly-once semantics, tambahkan mekanisme idempotency pada API standard.
- HTTP `201` vs `200`, identity provider, role mapping, rate limit, database engine, dan persetujuan target SLA masih TBD.

## References

- [Register keputusan terbuka Sprint 2](../../open-decisions.md) — definisi dan kriteria penutupan D-01–D-18.

- [Sprint 2 BRD](../../../BRD.md) — BR-019, pengelolaan kategori layanan.
- [Service Category ERD](../../../erd/service-category-erd.md) — atribut kategori dan kandidat uniqueness/index/default serta kebutuhan audit.
- [get-list-service-categories.md](./get-list-service-categories.md) — kontrak daftar kategori dan response summary.
- [get-detail-service-category.md](./get-detail-service-category.md) — kontrak pembacaan kategori detail.
