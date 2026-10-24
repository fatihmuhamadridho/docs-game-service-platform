# Admin Dashboard - Update Role Interface Agreement

> **Document Type:** IFA / API Contract  
> **Status:** Draft  
> **Service / Domain:** Admin Dashboard — Account & Role Management  
> **Consumer / Partner:** JokiHub Admin Dashboard (authorized administrator)

## Design

![Sprint 1 dashboard design](../../../design/dashboard.png)

## Sequence Diagram

```mermaid
sequenceDiagram
    actor Admin as Administrator berizin mengelola role
    participant UI as Admin Dashboard UI
    participant API as Admin Account & Role API
    participant DB as Identity / Authorization Database
    Admin->>UI: Ubah field role dan simpan
    UI->>API: PATCH /api/admin/roles/v1/update/{roleId} + cookie/token admin + field berubah
    API->>API: Autentikasi dan otorisasi izin role.update
    alt Tidak terautentikasi atau tidak berizin
        API-->>UI: 401 ROLB01001 atau 403 ROLB01002, data null
        UI-->>Admin: Tampilkan pesan akses
    else Berizin
        API->>API: Validasi roleId dan payload parsial
        alt ID/payload tidak valid
            API-->>UI: 400 ROLB02001, data null
            UI-->>Admin: Tampilkan kesalahan validasi
        else Payload valid
            API->>DB: Ambil role dan metadata proteksi
            alt Role tidak ditemukan atau sudah dihapus
                DB-->>API: Record tidak tersedia
                API-->>UI: 404 ROLB02004, data null
                UI-->>Admin: Tampilkan bahwa role tidak ditemukan
            else Role system/built-in terlindungi
                DB-->>API: Role tidak dapat diedit
                API-->>UI: 409 ROLB02005, data null
                UI-->>Admin: Tampilkan bahwa role dilindungi
            else Role dapat diedit
                API->>DB: Validasi keunikan name/code dan keanggotaan permission aktif pada katalog sistem
                alt Name/code duplikat
                    DB-->>API: Konflik role
                    API-->>UI: 409 ROLB02002, data null
                    UI-->>Admin: Tampilkan konflik nama/kode
                else Permission tidak terdaftar atau tidak aktif
                    DB-->>API: Permission tidak terdaftar atau tidak aktif
                    API-->>UI: 400 ROLB02003 atau 403 ROLB01002, data null
                    UI-->>Admin: Tampilkan permission yang perlu diperbaiki
                else Semua valid
                    API->>API: Bandingkan state hasil normalisasi; permissions dibandingkan sebagai set
                    alt Tidak ada perubahan efektif
                        API-->>UI: 200 ROLS00000, data null tanpa write atau perubahan updatedAt
                        UI-->>Admin: Tampilkan role tanpa perubahan
                    else Ada perubahan efektif
                        API->>DB: Simpan perubahan role dan relasi permission secara atomik
                        alt Penyimpanan berhasil
                            DB-->>API: Snapshot role terbaru
                            API-->>UI: 200 ROLS00000, data null
                            UI->>API: GET daftar/detail role setelah mutasi sukses
                            API-->>UI: Data role terbaru melalui response read
                            UI-->>Admin: Tampilkan role yang diperbarui
                        else Database gagal
                            DB-->>API: Kegagalan transaksi
                            API-->>UI: 500 ROLT03001, data null
                            UI-->>Admin: Tampilkan kegagalan dan opsi coba lagi
                        end
                    end
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

| Component          | Value                                                                                                                                                                                                                                                                                                    |
| ------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Description        | Memperbarui metadata dan/atau permission pada satu custom role. Role system SUPER_ADMIN tidak dapat diubah.                                                                                                                                                                                              |
| Resource URL       | https://{host}/api/admin/roles/v1/update/{roleId}                                                                                                                                                                                                                                                        |
| Transport Protocol | HTTPS, REST                                                                                                                                                                                                                                                                                              |
| Request Verb       | PATCH                                                                                                                                                                                                                                                                                                    |
| Content Type       | application/json                                                                                                                                                                                                                                                                                         |
| Interaction Type   | Synchronous                                                                                                                                                                                                                                                                                              |
| Security Policy    | Memerlukan cookie access admin valid, akun ACTIVE, dan session family belum dicabut, role Super Admin, dan permission `role.update` dari [katalog permission Sprint 1](./permission-model.md). Semua permission aktif pada katalog dapat dipilih untuk custom role. Sprint 1 tidak mencatat audit event. |

### Path / Endpoint

| Section        | Value                               |
| -------------- | ----------------------------------- |
| Host / Domain  | {host} (environment-specific)       |
| Service Name   | Admin Role Management               |
| Version        | v1                                  |
| Resource       | /admin/roles/v1/update/{roleId}     |
| Final Endpoint | /api/admin/roles/v1/update/{roleId} |

### API Versioning

- **Decision:** Tetapkan kontrak API v1 untuk Sprint 1.
- **Endpoint:** PATCH /api/admin/roles/v1/update/{roleId}
- **Backward Compatibility:** Path dan method mengikuti kontrak v1. Permission memakai key dari katalog Sprint 1.
- **Reason:** Menetapkan pembaruan role dengan kebijakan permission Sprint 1.

## Business Rules

**Rate limit:** 1.200 request/IP/menit dan 60 request/admin/menit. Pelampauan mengembalikan HTTP 429 dengan `Retry-After`.

**Autentikasi bersama:** Setiap request dashboard wajib memakai cookie access admin dari backend, Origin yang exact-match dengan allowlist, dan X-CSRF-Token. Backend memeriksa session family aktif serta status akun ACTIVE/non-deleted pada setiap autentikasi. Jika akun INACTIVE, request ditolak pada pemeriksaan berikutnya dan session family aktif dicabut. Setiap endpoint tetap memerlukan permission sesuai metode. Lihat [IFA login](../auth/login.md), [refresh token](../auth/refresh-token.md), dan [logout](../auth/logout.md).

1. Hanya Super Admin dengan cookie access admin valid, akun ACTIVE, dan session family belum dicabut dan permission `role.update` yang dapat mengubah custom role. `SUPER_ADMIN` selalu ditolak sebagai target perubahan.
2. `roleId` mengidentifikasi role yang dituju dan tidak dapat diubah.
3. Request bersifat parsial: field yang tidak dikirim mempertahankan nilai tersimpan. Field yang dikirim sebagai `null` hanya boleh mengosongkan `description`; `name`, `code`, `status`, dan `permissions` tidak menerima `null`.
4. Minimal satu field mutable wajib dikirim. Field immutable seperti `id`, `createdAt`, dan `updatedAt` tidak boleh dikirim.
5. `name` yang dinormalisasi trim dan case-insensitive harus unik selain terhadap role yang sedang diperbarui. `code` uppercase harus unik selain terhadap role yang sama.
6. Jika `permissions` dikirim, seluruh daftar diganti secara atomik. Semua key harus terdaftar, aktif, dan unik. Super Admin dapat memilih seluruh key aktif di katalog. Permission yang tidak dikirim tetap tidak berubah.
7. `SUPER_ADMIN` (`Super Admin`) adalah satu-satunya role hasil seed Sprint 1, ditandai `is_system=true`, dan tidak dapat diubah melalui endpoint ini. Semua field, termasuk status, permission, nama, dan deskripsi, immutable. Setiap upaya mengubahnya ditolak dengan `ROLB02005`.
8. Perubahan status ke `INACTIVE` ditolak bila role masih dipakai akun aktif. Penonaktifan harus didahului perubahan assignment melalui proses yang tersedia; role assignment tercakup di model ERD.
9. Perubahan permission atau status berlaku pada request dashboard berikutnya. Backend membaca state otorisasi terkini dan tidak memakai cache permission yang stale.
10. Seluruh perubahan metadata dan relasi permission harus atomik. Jika satu validasi gagal, tidak ada perubahan parsial.
11. `updatedAt` diperbarui dalam UTC hanya bila nilai efektif metadata atau grant berubah. Request no-op tetap sukses tanpa write dan tanpa mengubah `updatedAt`. Sprint 1 tidak mencatat audit event sesuai BRD.
12. Sprint 1 tidak memakai optimistic locking (`ETag`, `If-Match`, atau version field); concurrent perubahan efektif memakai last-write-wins dan update yang mengubah state memperbarui `updatedAt`.

## Request Headers

| Header   | E/N/T | Type   | M/O/C | Max Length | Description                                                                                                | Example                             |
| -------- | ----- | ------ | ----- | ---------: | ---------------------------------------------------------------------------------------------------------- | ----------------------------------- |
| `Cookie` | N     | String | M     |       4096 | Browser mengirim cookie access admin secara otomatis; token tidak dikirim melalui Authorization atau body. | `__Host-jokihub-admin-access={JWT}` |

| `Origin` | N | String | M | 2048 | Origin dashboard harus exact-match dengan allowlist API. | `https://dashboard.example.test` |
| `X-CSRF-Token` | N | String | M | 128 | CSRF token sinkronisasi untuk request cookie-authenticated. | `{csrf-token}` |
| `Content-Type` | N | String | M | 100 | Tipe media request. | `application/json` |
| `Accept` | N | String | O | 100 | Tipe media response yang diinginkan; default JSON. | `application/json` |
| `X-Request-Id` | N | String | O | 100 | ID korelasi request untuk tracing/support. | `req-21d6b8` |

## Request Parameters

| Parameter | E/N/T | Type   | M/O/C | Max Length | Description                             | Example       |
| --------- | ----- | ------ | ----- | ---------: | --------------------------------------- | ------------- |
| `roleId`  | N     | String | M     |         64 | ID immutable role yang akan diperbarui. | `role_01JXYZ` |

## Request Query Parameters

None.

## Request Body

| Attribute     | E/N/T | Type            | M/O/C | Max Length | Description                                                                          | Example                         |
| ------------- | ----- | --------------- | ----- | ---------: | ------------------------------------------------------------------------------------ | ------------------------------- |
| `name`        | N     | String          | O     |        100 | Nama tampilan role; trim sebelum disimpan.                                           | `Senior Support Agent`          |
| `code`        | N     | String          | O     |         50 | Kode role unik uppercase; .                                                          | `SENIOR_SUPPORT_AGENT`          |
| `description` | N     | String/null     | O     |        500 | Deskripsi role; `null` menghapus deskripsi.                                          | `Menangani eskalasi pelanggan.` |
| `status`      | N     | String          | O     |         20 | Status role kustom yang diterima: `ACTIVE` atau `INACTIVE`. `SUPER_ADMIN` immutable. | `ACTIVE`                        |
| `permissions` | N     | Array of String | O     |  100 items | Daftar key permission Sprint 1 pengganti; bila dikirim minimal satu key.             | `["role.read", "game.read"]`    |

### Example Request Body

```json
{
  "name": "Senior Support Agent",
  "description": "Menangani eskalasi pelanggan.",
  "permissions": ["role.read", "game.read"]
}
```

### Request Validation

1. Request harus membawa cookie access admin valid, Origin dashboard yang diizinkan, CSRF token valid, akun ACTIVE/non-deleted, session family aktif, dan permission `role.update`. Kembalikan `ROLB01001` bila belum terautentikasi dan `ROLB01002` bila permission tidak tersedia.
2. `roleId` wajib diawali `role_`, memiliki suffix nonkosong, dan maksimal 64 karakter; nilai tidak valid mengembalikan `ROLB02001`.
3. Body harus object dan memuat minimal satu dari `name`, `code`, `description`, `status`, atau `permissions`. Body kosong mengembalikan `ROLB02001`.
4. `name`, bila dikirim, harus string non-empty setelah trim dan maksimal 100 karakter.
5. `code`, bila dikirim, harus string non-empty, maksimal 50 karakter, dinormalisasi uppercase, dan mengikuti `^[A-Z][A-Z0-9_]*$`.
6. `description`, bila dikirim, harus string atau `null` dan maksimal 500 karakter.
7. `status`, bila dikirim, harus `ACTIVE` atau `INACTIVE`; nilai lainnya mengembalikan `ROLB02001`.
8. `permissions`, bila dikirim, harus berupa array berisi 1 sampai 100 key permission unik dari katalog Sprint 1. Key di luar katalog atau tidak aktif mengembalikan `ROLB02003`.
9. Field tak dikenal atau immutable seperti `id`, `createdAt`, `updatedAt`, dan atribut protected ditolak dengan `ROLB02001`.
10. Jika `roleId` tidak ditemukan atau sudah dihapus, kembalikan `ROLB02004`. Role protected/system yang tidak dapat diubah mengembalikan `ROLB02005`.
11. Nama/kode duplikat mengembalikan `ROLB02002`. Kombinasi status/assignment yang tidak valid mengembalikan `ROLB02006`.
12. Validasi payload sebelum commit. Error validasi mengembalikan HTTP 400, konflik state/unik mengembalikan HTTP 409, dan kegagalan database mengembalikan HTTP 500; semua error memakai `data: null`.

## Response Body

| Attribute | E/N/T | Type   | M/O/C | Max Length | Description                                  | Example        |
| --------- | ----- | ------ | ----- | ---------: | -------------------------------------------- | -------------- |
| `code`    | N     | String | M     |         20 | Kode status service Sprint 1.                | `ROLS00000`    |
| `message` | N     | String | M     |        200 | Pesan hasil operasi.                         | `Role updated` |
| `data`    | N     | Null   | M     |          — | Selalu `null` untuk operasi mutasi Sprint 1. | `null`         |

### Success Example

```json
{
  "code": "ROLS00000",
  "message": "Role updated",
  "data": null
}
```

### Business Error Example

```json
{
  "code": "ROLB02002",
  "message": "Role name or code already exists",
  "data": null
}
```

### Technical Error Example

```json
{
  "code": "ROLT03001",
  "message": "Role service is temporarily unavailable",
  "data": null
}
```

## HTTP Status Codes

| HTTP Status | Usage                                                                                           |
| ----------- | ----------------------------------------------------------------------------------------------- |
| `200`       | Role berhasil diperbarui; response memiliki `data: null`.                                       |
| `400`       | Payload tidak valid atau permission key tidak dikenal/tidak aktif.                              |
| `401`       | Admin belum terautentikasi atau session tidak valid.                                            |
| `403`       | Admin tidak memiliki permission `role.update`, Origin tidak diizinkan, atau CSRF tidak valid.   |
| `404`       | Role tidak ditemukan, sudah dihapus, atau sudah dipurge.                                        |
| `409`       | Nama/kode sudah digunakan, `SUPER_ADMIN` diubah, atau role masih ditugaskan saat dinonaktifkan. |
| `500`       | Kegagalan service atau database identity/authorization yang tidak terduga.                      |
| `429`       | Rate limit terlampaui; response menyertakan header `Retry-After`.                               |

## Service Status Codes

Format: `[Service Prefix][Status Type][Category][Specific Error Code]`

| Code        | Type      | Category        | Meaning                                                               |
| ----------- | --------- | --------------- | --------------------------------------------------------------------- |
| `ROLS00000` | Success   | General         | Role berhasil diperbarui.                                             |
| `ROLB01001` | Business  | Authentication  | Administrator belum terautentikasi.                                   |
| `ROLB01002` | Business  | Authentication  | Administrator tidak memiliki permission `role.update`.                |
| `ROLB02001` | Business  | User validation | ID/body/field wajib, format, panjang, atau state request tidak valid. |
| `ROLB02002` | Business  | User validation | Nama atau kode role sudah digunakan role lain.                        |
| `ROLB02003` | Business  | User validation | Satu atau lebih permission tidak dikenal/tidak tersedia.              |
| `ROLB02004` | Business  | User validation | Role tidak ditemukan atau sudah dihapus.                              |
| `ROLB02005` | Business  | User validation | Role system/built-in dilindungi dan tidak dapat diperbarui.           |
| `ROLB02006` | Business  | User validation | Status `INACTIVE` ditolak selama role masih ditugaskan ke akun aktif. |
| `ROLT03001` | Technical | Database        | Role, relasi permission gagal disimpan/database tidak tersedia.       |

## API Exposure Classification

| Classification | Rationale                                                                                                         |
| -------------- | ----------------------------------------------------------------------------------------------------------------- |
| Public         | Route dashboard dapat diakses dari internet dan dilindungi session admin, permission, CORS allowlist, serta CSRF. |

## Testing & Documentation Scope

| Scope      | Required | Rationale / Handoff                                                                                                                                                                                                  |
| ---------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| NFT        | Yes      | API admin write baru. Uji update parsial, konflik nama/kode, perubahan permission/status, role protected, assignment aktif, atomicity, dan beban 50 VUser / 20 request per detik; ukur CPU/memory terhadap baseline. |
| VA/Pentest | Yes      | API mengubah kontrol akses. Uji authentication/authorization, pemilihan permission dari seluruh katalog, privilege escalation, mass assignment, IDOR, injection, concurrent update, cache invalidation.              |

## Assumptions

- Custom role `status` dan `permissions` mengikuti model role; `SUPER_ADMIN` immutable sesuai ERD dan tidak dapat diubah.
- Field `permissions` mengganti seluruh permission role saat dikirim; perilaku merge/incremental update tidak didukung oleh kontrak ini.
- Role aktif dengan assignment aktif tidak dapat dinonaktifkan atau dihapus. Perubahan hak akses berlaku pada request berikutnya karena backend membaca state otorisasi terkini; concurrency memakai last-write-wins tanpa optimistic locking. Perlindungan `SUPER_ADMIN` mengikuti ERD dan BRD.
- Audit event tidak dicatat pada Sprint 1. SLA mengikuti baseline BRD; HTTP status memakai mapping BRD. Target permission `role.update` mengikuti katalog Sprint 1.
- Cakupan hanya memperbarui definisi role; assignment akun, create/list/detail/delete, dan CRUD permission catalog berada di luar API ini.

## References

- `./get-list-roles.md` — field role, status, permission keys, dan envelope Sprint 1.
- `./get-detail-role.md` — kontrak membaca detail role dan permission katalog.
- `./delete-role.md` — protected-role dan assignment checks Sprint 1.
- `docs-game-service-platform/sprint_1/solutioning/TEMPLATE.md` — template kontrak API sprint.
