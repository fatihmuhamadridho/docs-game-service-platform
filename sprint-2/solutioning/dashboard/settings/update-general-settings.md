# Admin Dashboard - Update General Settings Interface Agreement

> **Document Type:** IFA / API Contract  
> **Status:** Draft  
> **Service / Domain:** Admin Dashboard — Platform Settings  
> **Consumer / Partner:** JokiHub Admin Dashboard (authorized administrator)

## Design

![Sprint 2 dashboard design](../../../design/dashboard.png)

## Sequence Diagram

```mermaid
sequenceDiagram
    actor Admin as Administrator
    participant UI as Admin Dashboard UI
    participant API as Platform Settings API
    participant DB as Settings Database
    participant Cache as Storefront Config Cache
    Admin->>UI: Ubah pengaturan umum dan simpan
    UI->>API: PUT /api/admin/settings/v1/general + seluruh object settings
    API->>API: Autentikasi dan otorisasi settings-manage
    alt Tidak terautentikasi
        API-->>UI: 400 SETB01001, data null
    else Tidak berizin
        API-->>UI: 400 SETB01002, data null
    else Berizin
        API->>API: Validasi semua field dan aturan lintas-field
        alt Payload tidak valid
            API-->>UI: 400 SETB02001, data null
        else Payload valid
            API->>DB: Simpan seluruh snapshot settings
            Note over API,DB: Source menulis activity event; atomicity/durability/failure policy BR-022 tetap TBD
            alt Database gagal
                DB-->>API: Kegagalan repository
                API-->>UI: 500 SETT03001, data null
            else Settings tersimpan
                DB-->>API: Snapshot settings tersimpan
                API->>Cache: Invalidate/publish versi konfigurasi baru (target TBD)
                alt Propagation terkonfirmasi
                    Cache-->>API: Konfigurasi baru aktif
                    API-->>UI: 200 SETS00000, data object settings
                else Propagation belum terkonfirmasi
                    Cache-->>API: Status belum diketahui
                    API-->>UI: 200 SETS00000, propagationStatus proposal menunggu persetujuan
                end
            end
        end
    end
```

## Performance / SLA Targets

| Success Rate                                                  | VUser                              | CPU / Memory                                                                | Response Time              |
| ------------------------------------------------------------- | ---------------------------------- | --------------------------------------------------------------------------- | -------------------------- |
| 99.9% monthly availability; valid-request server error ≤ 0.1% | 50 VUser / 20 request/s NFT target | Initial 2 vCPU / 4 GiB per instance; CPU ≤ 70%, memory ≤ 75% at target load | Write p95 ≤ 1 s; p99 ≤ 2 s |

## General Information

| Component          | Value                                                             |
| ------------------ | ----------------------------------------------------------------- |
| Description        | Mengganti snapshot pengaturan umum platform dari Admin Dashboard. |
| Resource URL       | `https://{host}/api/admin/settings/v1/general`                    |
| Transport Protocol | HTTPS, REST                                                       |
| Request Verb       | `PUT`                                                             |
| Content Type       | `application/json`                                                |
| Interaction Type   | Synchronous; propagation may be asynchronous (target TBD)         |
| Security Policy    | Admin authentication dan permission `settings-manage`.            |

### Path / Endpoint

| Section        | Value                            |
| -------------- | -------------------------------- |
| Host / Domain  | `{host}` (environment-specific)  |
| Service Name   | Platform Settings                |
| Version        | `v1`                             |
| Resource       | `/admin/settings/v1/general`     |
| Final Endpoint | `/api/admin/settings/v1/general` |

### API Versioning

- **Decision:** Existing Endpoint / target enhancement.
- **Existing source route:** `PUT /api/admin/settings/v1/general` tercatat pada source inventory Sprint 3.
- **Proposed target endpoint:** `PUT /api/admin/settings/v1/general`; method/path dipertahankan.
- **Backward Compatibility:** PUT mewajibkan sepuluh field settings termasuk `socialLinks` dan `businessHours`, serta permission `settings-manage`. Target envelope mengikuti pola Sprint 1; HTTP mapping, concurrency, dan propagation memerlukan keputusan API owner.
- **Reason:** Admin Dashboard perlu menyimpan perubahan konfigurasi umum platform.

## Business Rules

1. Hanya administrator terautentikasi dengan permission `settings-manage` dapat memperbarui pengaturan, mengikuti route source saat ini.
2. `PUT` mengganti seluruh snapshot mutable; seluruh field kontrak wajib hadir. Partial update tidak termasuk.
3. `siteName` wajib dan tidak boleh kosong setelah trim. String disimpan sebagai teks biasa dan di-escape saat render.
4. `siteDescription` boleh kosong/null; batas target 500 karakter perlu diselaraskan dengan keputusan produk bila berbeda dari source.
5. `contactEmail` boleh string kosong; nilai non-empty mengikuti validasi dasar email source. Batas maksimum belum ditetapkan source.
6. `contactWhatsapp` mengikuti normalisasi source dan pola nomor internasional `^\\+?[1-9]\\d{7,14}$`.
7. `socialLinks` wajib berupa array maksimal 12 entri; setiap `label` wajib non-empty maksimal 40 karakter dan setiap `url` wajib HTTPS maksimal 255 karakter.
8. `businessHours` wajib hadir, menerima string/null maksimal 160 karakter.
9. `maintenanceMode` dan `allowRegistration` harus boolean JSON.
10. `defaultCurrency` saat ini hanya menerima `IDR`; `timezone` mengikuti `Asia/Jakarta`, `Asia/Makassar`, atau `Asia/Jayapura`.
11. Source menaikkan `version`, menetapkan `updatedAt`, dan menulis activity event. Atomicity audit, delivery guarantee, dan failure policy belum disepakati; jangan mengklaim transaksi atomik sebelum D-07/D-08 disetujui.
12. Cache/config propagation adalah target yang belum diputuskan. Semantics response saat persistence berhasil namun propagation belum selesai juga memerlukan persetujuan.
13. Source tidak membuktikan optimistic concurrency. `If-Match`, stale-write behavior, last-write-wins, serta dampak retry/no-op terhadap version dan audit masih TBD.
14. BR-022 mewajibkan actor, action, target, outcome, dan time serta redaksi data sensitif yang tidak diperlukan. Jangan mencatat token/session secret.

## Request Headers

| Header         | E/N/T | Type   | M/O/C | Max Length | Description                                                                                                | Example                             |
| -------------- | ----- | ------ | ----- | ---------: | ---------------------------------------------------------------------------------------------------------- | ----------------------------------- |
| `Cookie`       | N     | String | M     |       4096 | Browser mengirim cookie access admin secara otomatis; token tidak dikirim melalui Authorization atau body. | `__Host-jokihub-admin-access={JWT}` |
| `Origin`       | N     | String | M     |        TBD | Origin dashboard harus cocok dengan allowlist.                                                             | `https://admin.example.test`        |
| `X-CSRF-Token` | N     | String | M     |        TBD | Token CSRF untuk request admin cookie-authenticated.                                                       | `{csrf-token}`                      |
| `Content-Type` | N     | String | M     |        100 | Tipe media payload.                                                                                        | `application/json`                  |
| `Accept`       | N     | String | O     |        100 | Tipe media response yang diinginkan; default JSON.                                                         | `application/json`                  |
| `X-Request-Id` | N     | String | O     |        100 | ID korelasi untuk audit/support.                                                                           | `req-71c1ab`                        |
| `If-Match`     | N     | String | C     |        200 | Version/ETag untuk optimistic concurrency bila mekanisme disetujui.                                        | `"settings-v12"`                    |

## Request Parameters

None.

## Request Query Parameters

None.

## Request Body

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

1. Request wajib berasal dari administrator terautentikasi dengan permission `settings-manage`.
2. Body wajib berupa JSON object berisi seluruh field kontrak. Body kosong, field hilang, tipe salah, atau field tak dikenal menghasilkan `SETB02001`.
3. `siteName` harus string, trim, non-empty, maksimal 100 karakter.
4. `siteDescription` harus string/null dengan maksimal 500 karakter; markup disimpan sebagai teks biasa dan di-escape saat render.
5. `contactEmail` wajib hadir; source menerima string kosong atau validasi dasar email tanpa batas maksimum eksplisit.
6. `contactWhatsapp` wajib hadir; source menormalisasi whitespace/BOM/tanda kurung/tanda hubung dan mensyaratkan pola `^\\+?[1-9]\\d{7,14}$`.
7. `socialLinks` wajib array maksimal 12 objek; tiap `label` wajib string trim non-empty maksimal 40 karakter dan tiap `url` wajib HTTPS maksimal 255 karakter.
8. `businessHours` wajib hadir sebagai string/null dengan panjang maksimal 160 karakter.
9. `maintenanceMode` dan `allowRegistration` harus boolean JSON, bukan string atau angka.
10. `defaultCurrency` harus `IDR`; nilai lain ditolak sesuai source contract saat ini.
11. `timezone` harus anggota allowlist IANA: `Asia/Jakarta`, `Asia/Makassar`, atau `Asia/Jayapura`.
12. Jika optimistic concurrency disetujui, `If-Match` dapat diwajibkan; stale version memerlukan status yang ditetapkan D-06/D-11. Source saat ini hanya membuktikan version increment.
13. Validasi seluruh object sebelum persistence. HTTP/service-code mapping untuk kegagalan database tetap menunggu D-11.
14. Kegagalan propagation pasca-persistence tidak boleh memberi kesan bahwa perubahan belum tersimpan. Response/status retry yang sesuai masih menunggu persetujuan; `propagationStatus` belum menjadi kontrak.

## Response Body

| Attribute                  | E/N/T | Type        | M/O/C | Max Length | Description                                                   | Example                                                  |
| -------------------------- | ----- | ----------- | ----- | ---------: | ------------------------------------------------------------- | -------------------------------------------------------- |
| `code`                     | N     | String      | M     |         20 | Service code.                                                 | `SETS00000`                                              |
| `message`                  | N     | String      | O     |        200 | Pesan hasil untuk consumer.                                   | `Settings updated`                                       |
| `data`                     | N     | Object/null | M     |          — | Object settings; `null` saat error.                           | `{}`                                                     |
| `data.siteName`            | N     | String      | C     |        100 | Nama platform tersimpan.                                      | `JokiHub`                                                |
| `data.siteDescription`     | N     | String/null | C     |        500 | Deskripsi platform tersimpan.                                 | `Solusi top up game...`                                  |
| `data.contactEmail`        | N     | String      | C     |        TBD | Email bantuan.                                                | `support@example.test`                                   |
| `data.contactWhatsapp`     | N     | String      | C     |        TBD | Nomor bantuan setelah normalisasi source.                     | `+19995550123`                                           |
| `data.socialLinks`         | N     | Array       | C     |          — | Link publik yang diizinkan, `{label,url}`.                    | `[ {"label":"Instagram","url":"https://example.test"} ]` |
| `data.socialLinks[].label` | N     | String      | C     |         40 | Label tautan.                                                 | `Instagram`                                              |
| `data.socialLinks[].url`   | N     | String      | C     |        255 | URL HTTPS.                                                    | `https://example.test`                                   |
| `data.businessHours`       | N     | String/null | C     |        160 | Jam operasional display atau null.                            | `Setiap hari, 09.00–21.00 WIB`                           |
| `data.maintenanceMode`     | N     | Boolean     | C     |          — | Status mode pemeliharaan.                                     | `false`                                                  |
| `data.allowRegistration`   | N     | Boolean     | C     |          — | Status pendaftaran member.                                    | `true`                                                   |
| `data.defaultCurrency`     | N     | String      | C     |          3 | Mata uang default.                                            | `IDR`                                                    |
| `data.timezone`            | N     | String      | C     |         64 | IANA timezone.                                                | `Asia/Jakarta`                                           |
| `data.version`             | N     | Integer     | M     |          — | Metadata version; penempatan pada `data` adalah target.       | `13`                                                     |
| `data.updatedAt`           | N     | String/null | M     |         35 | ISO 8601 UTC atau null; penempatan pada `data` adalah target. | `2026-10-12T09:45:00Z`                                   |

### Success Example

```json
{
  "code": "SETS00000",
  "message": "Settings updated",
  "data": {
    "siteName": "JokiHub",
    "siteDescription": "Solusi top up game, joki, dan kebutuhan digital.",
    "contactEmail": "support@example.test",
    "contactWhatsapp": "+19995550123",
    "socialLinks": [{ "label": "Instagram", "url": "https://example.test" }],
    "businessHours": "Setiap hari, 09.00–21.00 WIB",
    "maintenanceMode": false,
    "allowRegistration": true,
    "defaultCurrency": "IDR",
    "timezone": "Asia/Jakarta",
    "version": 13,
    "updatedAt": "2026-10-12T09:45:00Z"
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

| HTTP Status | Usage                                                                                                                                     |
| ----------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| `200`       | Settings berhasil disimpan. Perilaku response saat propagation tertunda masih menunggu keputusan.                                         |
| `400`       | Akses admin ditolak, payload tidak valid, atau version stale sesuai baseline response project; konfirmasi mapping `401`/`403`/`412` D-11. |
| `500`       | Kegagalan database atau service yang tidak terduga.                                                                                       |

## Service Status Codes

Format: `[Service Prefix][Status Type][Category][Specific Error Code]`

| Code        | Type      | Category        | Meaning                                                                |
| ----------- | --------- | --------------- | ---------------------------------------------------------------------- |
| `SETS00000` | Success   | General         | Settings berhasil diperbarui.                                          |
| `SETB01001` | Business  | Authentication  | Administrator belum terautentikasi.                                    |
| `SETB01002` | Business  | Authentication  | Administrator tidak memiliki izin memperbarui settings.                |
| `SETB02001` | Business  | User validation | Field settings tidak lengkap, format salah, atau nilai tidak didukung. |
| `SETB02002` | Business  | User validation | Version settings stale; pemakaian kode ini menunggu persetujuan.       |
| `SETT03001` | Technical | Database        | Persistence settings gagal/database tidak tersedia.                    |

## API Exposure Classification

| Classification            | Rationale                                                                                                                |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| TBD (deployment exposure) | Endpoint mengubah konfigurasi platform dan perilaku storefront. Jika internet-accessible, klasifikasikan sebagai Public. |

## Testing & Documentation Scope

| Scope      | Required | Rationale / Handoff                                                                                                                                                                             |
| ---------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| NFT        | Yes      | Uji validasi seluruh settings, persistence, audit, propagation target, admin concurrency, dan failure path. VUser/CPU/memory/response-time/TPS masih TBD.                                       |
| VA/Pentest | Yes      | Uji authentication/authorization, mass assignment, URL validation, stored/reflected XSS pada teks, CSRF, privilege bypass, concurrency overwrite, serta audit attribution dan secret redaction. |

## Assumptions

- Route PUT `/api/admin/settings/v1/general` sudah tercatat pada source inventory Sprint 3.
- Target mempertahankan root envelope `{code,message,data}` sesuai pola Sprint 1; HTTP/error mapping tetap memerlukan keputusan D-11.
- `defaultCurrency` hanya `IDR`; timezone mengikuti tiga pilihan yang tercatat. Perubahan daftar nilai memerlukan koordinasi consumer.
- Authentication/permission implementation, storage, audit atomicity, cache propagation, optimistic concurrency, default bootstrap values, SLA, dan HTTP status mapping masih TBD.
- Cakupan hanya perubahan pengaturan umum; pembayaran, pajak, biaya layanan, SEO, banner, dan konfigurasi provider tidak termasuk.

## References

- [Sprint 2 BRD](../../../BRD.md) — BR-020 field/settings scope and BR-022 audit requirement.
- [Platform settings ERD](../../../erd/platform-settings-erd.md) — logical settings data proposal and unresolved physical mapping.
- [Go admin dashboard source inventory](../../../../sprint-3/solutioning/backend-go/dashboard-source-ifa.md#route-to-handler-coverage) — current admin route/permission/field/response behavior.
- [GET general settings IFA](./get-general-settings.md) — companion read contract.
- [Sprint 2 decision register](../../../../README.md#keputusan-yang-menghalangi-finalisasi) — open decisions D-05/D-06/D-07/D-08/D-09/D-11.
