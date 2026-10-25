# Admin Dashboard - General Settings Interface Agreement

> **Document Type:** IFA / API Contract  
> **Status:** Draft  
> **Service / Domain:** Admin Dashboard — Platform Settings  
> **Consumer / Partner:** JokiHub Admin Dashboard (authorized administrator)

## Design

Kontrak ini menilai API pengaturan umum yang sudah terdaftar pada `GET` dan `PUT /api/admin/settings/v1/general`, lalu memisahkan perilaku sumber dari usulan target. Field request dan permission target mengikuti source inventory (`settings-manage`, sepuluh field settings), sedangkan HTTP/status mapping, concurrency, audit guarantee, dan propagation masih berupa proposal yang perlu diselaraskan sebelum disebut kontrak final.

> **Status kontrak target:** Struktur response mengikuti baseline Sprint 1 `{code,message,data}`. Source admin inventory memakai bentuk root yang sama; HTTP/auth mapping dan error status tetap perlu diselaraskan pada D-11. Permission `settings-manage` tercatat pada source; grant role Sprint 2 direview bersama D-18.

## Sequence Diagram

```mermaid
sequenceDiagram
    actor Admin as Administrator
    participant UI as Admin Dashboard UI
    participant API as Platform Settings API
    participant DB as Settings Database
    participant Cache as Storefront Config Cache
    Admin->>UI: Buka Pengaturan Umum
    UI->>API: GET /api/admin/settings/v1/general + kredensial admin
    API->>API: Autentikasi dan otorisasi settings-manage
    alt Tidak terautentikasi
        API-->>UI: 400 SETB01001, data null
    else Tidak berizin
        API-->>UI: 400 SETB01002, data null
    else Berizin
        API->>DB: Ambil pengaturan umum aktif
        alt Pengaturan ditemukan
            DB-->>API: Snapshot settings
        else Belum ada konfigurasi
            DB-->>API: Tidak ada record
            API->>API: Gunakan konfigurasi default terkelola
        end
        API-->>UI: 200 SETS00000, target data object includes version/updatedAt
    end
    Admin->>UI: Ubah field dan simpan
    UI->>API: PUT /api/admin/settings/v1/general + seluruh object settings
    API->>API: Autentikasi dan otorisasi settings-manage
    API->>API: Validasi seluruh field dan aturan lintas-field
    alt Request tidak terautentikasi
        API-->>UI: 400 SETB01001, data null
    else Pemanggil tidak berizin
        API-->>UI: 400 SETB01002, data null
    else Payload tidak valid
        API-->>UI: 400 SETB02001, data null
    else Payload valid
        API->>DB: Simpan seluruh settings
        Note over API,DB: Source settings menulis activity event; atomicity/durability/failure policy BR-022 tetap TBD
        alt Database gagal
            DB-->>API: Kegagalan repository
            API-->>UI: 500 SETT03001, data null
        else Settings tersimpan
            DB-->>API: Snapshot settings tersimpan
            API->>Cache: Invalidate/publish versi konfigurasi baru
            alt Config berhasil dipropagasi
                Cache-->>API: Konfigurasi baru aktif
                API-->>UI: 200 SETS00000, proposal-only propagationStatus=APPLIED
            else Propagation tertunda/gagal
                Cache-->>API: Belum terkonfirmasi
                API-->>UI: 200 SETS00000, proposal-only propagationStatus=PENDING
            end
        end
    end
```

## Performance / SLA Targets

| Success Rate                                     | VUser                                       | CPU / Memory                        | Response Time                                          |
| ------------------------------------------------ | ------------------------------------------- | ----------------------------------- | ------------------------------------------------------ |
| TBD; confirm criticality and availability target | TBD; confirm peak concurrent administrators | TBD; set from capacity estimate/NFT | TBD; include config propagation target and measurement |

## General Information

| Component          | Value                                                                                                                                                                                    |
| ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Description        | Membaca dan memperbarui pengaturan umum platform yang digunakan Admin Dashboard dan storefront.                                                                                          |
| Resource URL       | `https://{host}/api/admin/settings/v1/general`                                                                                                                                           |
| Transport Protocol | HTTPS, REST                                                                                                                                                                              |
| Request Verb       | `GET` untuk baca; `PUT` untuk mengganti snapshot settings                                                                                                                                |
| Content Type       | `application/json` untuk `PUT`; tidak diperlukan untuk `GET`                                                                                                                             |
| Interaction Type   | Synchronous; config propagation dapat asynchronous                                                                                                                                       |
| Security Policy    | Admin authentication dan permission `settings-manage` yang sudah tercatat di source. Detail cookie/CSRF, status HTTP, dan envelope target tetap mengikuti keputusan kompatibilitas D-11. |

### Path / Endpoint

| Section        | Value                            |
| -------------- | -------------------------------- |
| Host / Domain  | `{host}` (environment-specific)  |
| Service Name   | Platform Settings                |
| Version        | `v1`                             |
| Resource       | `/admin/settings/v1/general`     |
| Final Endpoint | `/api/admin/settings/v1/general` |

### API Versioning

- **Decision:** Existing Endpoint / target enhancement
- **Existing source route:** `GET, PUT /api/admin/settings/v1/general` tercatat pada source inventory Sprint 3.
- **Proposed target endpoint:** `GET, PUT /api/admin/settings/v1/general`; method/path dipertahankan, tetapi perubahan wire behavior menunggu keputusan API owner.
- **Backward Compatibility:** Pertahankan path dan field source: `PUT` mewajibkan sepuluh field settings termasuk `socialLinks` dan `businessHours`, serta permission `settings-manage`. Root envelope target mengikuti Sprint 1; HTTP mapping, concurrency, dan propagation belum disetujui dan memerlukan keputusan/migrasi API owner.
- **Reason:** Admin Dashboard memerlukan konfigurasi persisten untuk menampilkan dan mengubah pengaturan platform.

## Business Rules

1. Hanya administrator terautentikasi dengan permission `settings-manage` dapat membaca atau memperbarui pengaturan, mengikuti route source saat ini. Permission granular terpisah belum didukung source dan memerlukan keputusan RBAC bila diinginkan.
2. Resource merepresentasikan satu snapshot konfigurasi umum platform. `GET` mengembalikan seluruh field yang dapat dikelola UI.
3. `PUT` mengganti seluruh snapshot mutable. Semua field harus dikirim; field yang tidak ada tidak diam-diam di-reset. Gunakan `PATCH` bila product menginginkan partial update.
4. `siteName` wajib dan tidak boleh kosong setelah trim. String disimpan dalam bentuk teks biasa; escaping dilakukan saat render.
5. `siteDescription` boleh kosong/null sesuai kebijakan panjang; copy ini informasi storefront dan tidak boleh berisi HTML aktif.
6. `contactEmail` pada source boleh string kosong; nilai non-empty harus lolos validasi dasar email. `contactWhatsapp` mengikuti normalisasi dan pola nomor internasional source; aturan lebih ketat memerlukan persetujuan.
7. `socialLinks` adalah array wajib yang boleh kosong, maksimal 12 entri; setiap `label` wajib setelah trim, maksimal 40 karakter, dan setiap `url` harus HTTPS maksimal 255 karakter, mengikuti source contract saat ini.
8. `businessHours` wajib hadir pada `PUT`, menerima string/null maksimal 160 karakter; format dan makna zona waktu yang ditampilkan masih perlu keputusan produk.
9. `maintenanceMode` dan `allowRegistration` adalah boolean. Makna enforcement, cakupan maintenance, dan relasi ke provisioning admin belum ditetapkan oleh source route.
10. `defaultCurrency` saat ini menerima `IDR`; kepemilikan mata uang dan hubungan ke harga/checkout masih perlu keputusan produk.
11. `timezone` mengikuti enum source `Asia/Jakarta`, `Asia/Makassar`, atau `Asia/Jayapura`.
12. Source menyimpan settings, menaikkan `version`, menetapkan `updatedAt`, dan menulis activity event. Atomicity antara settings dan audit, delivery guarantee, serta perilaku kegagalan audit belum ditetapkan; target tidak boleh mengklaim transaksi atomik sebelum D-07/D-08 disetujui.
13. Setelah penyimpanan berhasil, cache/config storefront dapat di-invalidate atau dipublikasikan sebagai target; mekanisme, freshness, dan perilaku saat propagation gagal tetap TBD.
14. Source menaikkan `version`, tetapi tidak membuktikan optimistic concurrency. Perbandingan versi/ETag, stale-write response, last-write-wins policy, serta efek same-payload/no-op dan retry terhadap version/activity event tetap TBD.
15. BR-022 mewajibkan actor, action, target, outcome, dan time serta redaksi data sensitif yang tidak diperlukan. Field audit tambahan (termasuk changed fields/correlation ID) dan mekanisme delivery perlu disetujui; jangan menyimpan token/session secret.

## Request Headers

| Header         | E/N/T | Type   | M/O/C | Max Length | Description                                                                     | Example                      |
| -------------- | ----- | ------ | ----- | ---------: | ------------------------------------------------------------------------------- | ---------------------------- |
| `Cookie`       | E     | String | M     |        TBD | Session admin dikirim browser; nama dan atribut cookie mengikuti auth Sprint 1. | `{admin session cookie}`     |
| `Origin`       | N     | String | M     |        TBD | Origin dashboard harus cocok dengan allowlist.                                  | `https://admin.example.test` |
| `X-CSRF-Token` | N     | String | M     |        TBD | Token CSRF untuk request admin cookie-authenticated.                            | `{csrf-token}`               |
| `Content-Type` | N     | String | C     |        100 | Wajib untuk `PUT`; tipe media request.                                          | `application/json`           |
| `Accept`       | N     | String | O     |        100 | Tipe media response yang diinginkan; default JSON.                              | `application/json`           |
| `X-Request-Id` | N     | String | O     |        100 | ID korelasi untuk audit/support.                                                | `req-71c1ab`                 |
| `If-Match`     | N     | String | C     |        200 | Version/ETag settings untuk optimistic concurrency bila mekanisme disetujui.    | `"settings-v12"`             |

## Request Parameters

None.

## Request Query Parameters

None.

## Request Body

`GET` tidak menerima body. `PUT` mengirim seluruh object berikut.

| Attribute             | E/N/T | Type        | M/O/C | Max Length | Description                                                                               | Example                                                  |
| --------------------- | ----- | ----------- | ----- | ---------: | ----------------------------------------------------------------------------------------- | -------------------------------------------------------- |
| `siteName`            | N     | String      | M     |        100 | Nama platform, wajib non-empty setelah trim.                                              | `JokiHub`                                                |
| `siteDescription`     | N     | String/null | M     |        500 | Deskripsi platform; string kosong/null menghapus deskripsi.                               | `Solusi top up game, joki, dan kebutuhan digital.`       |
| `contactEmail`        | N     | String      | M     |        TBD | Email bantuan; source tidak menetapkan batas panjang eksplisit.                           | `support@example.test`                                   |
| `contactWhatsapp`     | N     | String      | M     |        TBD | Nomor bantuan; source menormalisasi karakter tertentu dan memvalidasi pola internasional. | `+19995550123`                                           |
| `socialLinks`         | N     | Array       | M     |          — | Array `{label,url}`; boleh kosong, maksimal 12 entri.                                     | `[ {"label":"Instagram","url":"https://example.test"} ]` |
| `socialLinks[].label` | N     | String      | M     |         40 | Label setelah trim tidak boleh kosong.                                                    | `Instagram`                                              |
| `socialLinks[].url`   | N     | String      | M     |        255 | URL HTTPS.                                                                                | `https://example.test`                                   |
| `businessHours`       | N     | String/null | M     |        160 | Jam operasional display; null diperbolehkan.                                              | `Setiap hari, 09.00–21.00 WIB`                           |
| `maintenanceMode`     | N     | Boolean     | M     |          — | Aktifkan mode pemeliharaan storefront.                                                    | `false`                                                  |
| `allowRegistration`   | N     | Boolean     | M     |          — | Izinkan pendaftaran akun member.                                                          | `true`                                                   |
| `defaultCurrency`     | N     | String      | M     |          3 | Mata uang default; saat ini hanya `IDR`.                                                  | `IDR`                                                    |
| `timezone`            | N     | String      | M     |         64 | IANA timezone untuk tampilan/waktu platform.                                              | `Asia/Jakarta`                                           |

### Example Request Body

```json
{
  "siteName": "JokiHub",
  "siteDescription": "Solusi top up game, joki, dan kebutuhan digital.",
  "contactEmail": "support@example.test",
  "contactWhatsapp": "+19995550123",
  "socialLinks": [{ "label": "Instagram", "url": "https://example.test" }],
  "businessHours": "Setiap hari, 09.00–21.00 WIB",
  "maintenanceMode": false,
  "allowRegistration": true,
  "defaultCurrency": "IDR",
  "timezone": "Asia/Jakarta"
}
```

### Request Validation

1. `GET` dan `PUT` memerlukan autentikasi serta permission `settings-manage` pada source. Target HTTP/service-code mapping untuk unauthorized/forbidden tetap proposal hingga D-11 disetujui.
2. `PUT` harus berisi JSON object dengan semua field kontrak. Body kosong, field wajib hilang, tipe salah, atau field tak dikenal mengembalikan `SETB02001`.
3. `siteName` harus string, trim, non-empty, dan maksimal 100 karakter.
4. `siteDescription` harus string/null dengan maksimal 500 karakter; karakter markup disimpan sebagai teks biasa dan di-escape saat render.
5. `contactEmail` wajib hadir; source menerima string kosong atau validasi dasar email tanpa batas maksimum eksplisit. Aturan target yang lebih ketat memerlukan persetujuan.
6. `contactWhatsapp` wajib hadir; source menormalisasi whitespace/BOM/tanda kurung/tanda hubung dan mensyaratkan pola `^\\+?[1-9]\\d{7,14}$`.
7. `socialLinks` wajib berupa array paling banyak 12 objek; tiap `label` wajib string trim non-empty maksimal 40 karakter dan tiap `url` wajib HTTPS maksimal 255 karakter.
8. `businessHours` wajib hadir sebagai string/null dengan panjang maksimal 160 karakter.
9. `maintenanceMode` dan `allowRegistration` harus boolean JSON, bukan string atau angka.
10. `defaultCurrency` harus `IDR`; nilai lain ditolak sesuai source contract saat ini.
11. `timezone` harus anggota allowlist IANA pada source: `Asia/Jakarta`, `Asia/Makassar`, atau `Asia/Jayapura`.
12. Bila optimistic concurrency disetujui, `If-Match` dapat diwajibkan dan stale version memerlukan status yang ditetapkan D-06/D-11; source saat ini hanya membuktikan version increment.
13. Validasi seluruh object sebelum persistence. Target HTTP/service-code mapping untuk kegagalan database tetap menunggu D-11.
14. Kegagalan propagation setelah persistence tidak boleh memberi kesan bahwa perubahan belum tersimpan. Target dapat mengembalikan snapshot tersimpan beserta `propagationStatus: PENDING` setelah response semantics dan retry disetujui. Field/status propagation ini usulan target saja; belum tercatat sebagai perilaku source maupun mekanisme yang tersedia.

## Response Body

Semua response target memakai envelope Sprint 1 `{code,message,data}`; `data` langsung berupa object settings beserta metadata. `GET` dan `PUT` sukses mengembalikan settings object langsung pada `data`; response `PUT` mengusulkan metadata update dan status propagation. `propagationStatus` beserta nilai `APPLIED`/`PENDING` adalah contoh proposal target saja; inventory source tidak mendokumentasikan field atau mekanisme tersebut.

| Attribute | E/N/T | Type        | M/O/C | Max Length | Description                         | Example              |
| --------- | ----- | ----------- | ----- | ---------: | ----------------------------------- | -------------------- |
| `code`    | N     | String      | M     |         20 | Service code.                       | `SETS00000`          |
| `message` | N     | String      | O     |        200 | Pesan hasil untuk consumer.         | `Settings retrieved` |
| `data`    | N     | Object/null | M     |          — | Object settings; `null` saat error. | `{}`                 |

| `data.siteName` | N | String | C | 100 | Nama platform tersimpan. | `JokiHub` |
| `data.siteDescription` | N | String/null | C | 500 | Deskripsi platform tersimpan. | `Solusi top up game...` |
| `data.contactEmail` | N | String | C | TBD | Email bantuan; source tidak menetapkan batas panjang eksplisit. | `support@example.test` |
| `data.contactWhatsapp` | N | String | C | TBD | Nomor bantuan setelah normalisasi source. | `+19995550123` |
| `data.socialLinks` | N | Array | C | — | Link publik yang diizinkan, `{label,url}`. | `[ {"label":"Instagram","url":"https://example.test"} ]` |
| `data.socialLinks[].label` | N | String | C | 40 | Label tautan. | `Instagram` |
| `data.socialLinks[].url` | N | String | C | 255 | URL HTTPS. | `https://example.test` |
| `data.businessHours` | N | String/null | C | 160 | Jam operasional display atau null. | `Setiap hari, 09.00–21.00 WIB` |
| `data.maintenanceMode` | N | Boolean | C | — | Status mode pemeliharaan. | `false` |
| `data.allowRegistration` | N | Boolean | C | — | Status pendaftaran member. | `true` |
| `data.defaultCurrency` | N | String | C | 3 | Mata uang default. | `IDR` |
| `data.timezone` | N | String | C | 64 | IANA timezone. | `Asia/Jakarta` |
| `data.version` | N | Integer | M | — | Metadata version. Source mengembalikannya sebagai sibling; target menaruhnya di dalam `data`. Stale-write check belum dibuktikan. | `12` |
| `data.updatedAt` | N | String/null | M | 35 | Metadata timestamp ISO 8601 UTC atau null bila belum ada row. Source mengembalikannya sebagai sibling; target menaruhnya di dalam `data`. | `2026-10-12T09:45:00Z` |
| `data.propagationStatus` | N | String | C | 20 | Status config propagation pada response `PUT`. | `APPLIED` |

### Success Example — GET

```json
{
  "code": "SETS00000",
  "message": "Settings retrieved",
  "data": {
    "siteName": "JokiHub",
    "siteDescription": "Solusi top up game, joki, dan kebutuhan digital.",
    "contactEmail": "support@example.test",
    "contactWhatsapp": "+19995550123",
    "socialLinks": [
      {
        "label": "Instagram",
        "url": "https://example.test"
      }
    ],
    "businessHours": "Setiap hari, 09.00–21.00 WIB",
    "maintenanceMode": false,
    "allowRegistration": true,
    "defaultCurrency": "IDR",
    "timezone": "Asia/Jakarta",
    "version": 12,
    "updatedAt": "2026-10-12T09:45:00Z"
  }
}
```

### Success Example — PUT

```json
{
  "code": "SETS00000",
  "message": "Settings updated",
  "data": {
    "siteName": "JokiHub",
    "siteDescription": "Solusi top up game, joki, dan kebutuhan digital.",
    "contactEmail": "support@example.test",
    "contactWhatsapp": "+19995550123",
    "socialLinks": [
      {
        "label": "Instagram",
        "url": "https://example.test"
      }
    ],
    "businessHours": "Setiap hari, 09.00–21.00 WIB",
    "maintenanceMode": false,
    "allowRegistration": true,
    "defaultCurrency": "IDR",
    "timezone": "Asia/Jakarta",
    "version": 13,
    "updatedAt": "2026-10-12T09:45:00Z",
    "propagationStatus": "APPLIED"
  }
}
```

### Business Error Example

```json
{
  "code": "SETB02001",
  "message": "One or more settings fields are invalid",
  "data": null
}
```

### Technical Error Example

```json
{
  "code": "SETT03001",
  "message": "Settings service is temporarily unavailable",
  "data": null
}
```

## HTTP Status Codes

| HTTP Status | Usage                                                                                                                                                                |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `200`       | Settings berhasil dibaca atau disimpan. `PUT` dapat mengembalikan `propagationStatus: PENDING` jika persistence sukses tetapi propagation belum terkonfirmasi.       |
| `400`       | Akses admin ditolak, payload tidak valid, atau version stale sesuai baseline response project. Konfirmasi apakah API standard mengharuskan `401`, `403`, atau `412`. |
| `500`       | Kegagalan database atau service yang tidak terduga. Kegagalan propagation pasca-persistensi dilaporkan sebagai pending dan diproses retry, bukan rollback semu.      |

## Service Status Codes

Format: `[Service Prefix][Status Type][Category][Specific Error Code]`

| Code        | Type      | Category        | Meaning                                                                |
| ----------- | --------- | --------------- | ---------------------------------------------------------------------- |
| `SETS00000` | Success   | General         | Settings berhasil dibaca atau diperbarui.                              |
| `SETB01001` | Business  | Authentication  | Administrator belum terautentikasi.                                    |
| `SETB01002` | Business  | Authentication  | Administrator tidak memiliki izin baca/perbarui settings.              |
| `SETB02001` | Business  | User validation | Field settings tidak lengkap, format salah, atau nilai tidak didukung. |
| `SETB02002` | Business  | User validation | Version settings stale; perubahan ditolak untuk mencegah overwrite.    |
| `SETT03001` | Technical | Database        | Persistence settings gagal/database tidak tersedia.                    |

## API Exposure Classification

| Classification            | Rationale                                                                                                                                                 |
| ------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| TBD (deployment exposure) | Endpoint mengubah konfigurasi platform dan perilaku storefront. Jika internet-accessible, klasifikasikan sebagai Public dan terapkan proteksi API publik. |

## Testing & Documentation Scope

| NFT / VA-Pentest handoff   | Value                                                                                                                                                                                  |
| -------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Service / module           | Platform Settings                                                                                                                                                                      |
| Endpoint                   | `GET and PUT /api/admin/settings/v1/general`                                                                                                                                           |
| Sample parameter / request | GET has no parameters; PUT sample uses `siteName="JokiHub"` and the Example Request Body                                                                                               |
| Sample response            | Success code `SETS00000`; use the complete Success Example below. Probe code `SETB01002`; use its corresponding error example below. HTTP mapping remains subject to D-11 where noted. |
| NFT CPU / memory target    | TBD; capacity evidence and approval required under D-12.                                                                                                                               |
| NFT TPS target             | TBD; traffic forecast and approval required under D-12.                                                                                                                                |

| Scope      | Required | Rationale / Handoff                                                                                                                                                                  |
| ---------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| NFT        | Yes      | Uji read/write settings, ukuran beban admin, dan propagation cache saat perubahan maintenance/registration. Target VUser, TPS, dan propagation latency TBD.                          |
| VA/Pentest | Yes      | Uji authentication/authorization, mass assignment, URL validation, stored/reflected XSS pada teks, CSRF sesuai auth, privilege bypass, concurrency overwrite, dan audit attribution. |

## Assumptions

- Path `GET` dan `PUT /api/admin/settings/v1/general` sudah ada. Target mempertahankan root `{code,message,data}`; HTTP/error mapping tetap perlu diselaraskan melalui D-11.
- UI saat ini mensimulasikan penyimpanan untuk sesi dan belum memiliki backend contract. Dokumen ini mengusulkan persistence server-side.
- `defaultCurrency` hanya `IDR`; timezone mengikuti tiga pilihan UI. Perubahan daftar nilai perlu koordinasi consumer.
- Authentication/permission model, settings storage, audit store, config cache/propagation, optimistic concurrency, default bootstrap values, SLA, dan HTTP status mapping masih TBD.
- Cakupan hanya pengaturan umum pada halaman admin; pengaturan pembayaran, pajak, biaya layanan, SEO, banner, dan konfigurasi provider tidak termasuk.

## Current Go Behavior vs Target

Admin GET/PUT saat ini tercatat dengan permission `settings-manage`. GET mengembalikan `settings` berisi `siteName`, `siteDescription`, `contactEmail`, `contactWhatsapp`, `socialLinks`, `businessHours`, `maintenanceMode`, `allowRegistration`, `defaultCurrency`, dan `timezone`, serta sibling `version` dan `updatedAt`; bila row belum ada, source mengembalikan defaults, version 0, dan timestamp null. PUT mewajibkan sepuluh field, menaikkan version (row baru mulai dari 1), menormalisasi string, dan menulis activity event. Perilaku request/error aktual dan response envelope top-level `{code,message,data}` dirinci dalam [dashboard route source IFA](../../../../sprint-3/solutioning/backend-go/dashboard-source-ifa.md#route-to-handler-coverage). Public `GET /api/settings/v1/public` memakai allowlist public `siteName`, `siteDescription`, `contactEmail`, `contactWhatsapp`, `socialLinks`, dan `businessHours`, fallback defaults, serta `Cache-Control: no-store` sesuai [public catalog source audit](../../public/catalog/source-audit-2026-10-23.md).

Target field dan permission pada revisi ini mengikuti request/response field set serta `settings-manage` yang tercatat pada source inventory. HTTP/error mapping, concurrency, propagation, dan semantics maintenance/registration belum disetujui. Source admin response memakai envelope root `{code,message,data}` dengan resource `settings` dan sibling `version`/`updatedAt`. Target mengikuti root envelope dan meletakkan settings object langsung pada `data`, dengan metadata target sebagai field resource. HTTP/error, concurrency, dan propagation masih memerlukan keputusan D-05/D-06/D-09/D-11.

## References

- [Sprint 2 BRD](../../../BRD.md) — BR-020 field/settings scope and BR-022 audit requirement.
- [Platform settings ERD](../../../erd/platform-settings-erd.md) — logical settings data proposal and unresolved physical mapping.
- [Go admin dashboard source inventory](../../../../sprint-3/solutioning/backend-go/dashboard-source-ifa.md#route-to-handler-coverage) — current admin route/permission/field/response behavior.
- [Public catalog source audit](../../public/catalog/source-audit-2026-10-23.md) — current public settings projection and cache behavior.
- [Sprint 2 decision register](../../README.md#keputusan-yang-menghalangi-finalisasi) — open decisions D-05/D-06/D-09/D-11.
