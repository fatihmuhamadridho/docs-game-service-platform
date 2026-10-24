# Sprint 1 — High-Level Design

## Stack Teknologi

| Komponen            | Teknologi | Tanggung jawab                                                                                      |
| ------------------- | --------- | --------------------------------------------------------------------------------------------------- |
| Platform deployment | Vercel    | Men-deploy dan menyajikan aplikasi Next.js serta backend API Golang.                                |
| Aplikasi web        | Next.js   | Menyediakan halaman publik dan dashboard administrator, lalu memanggil Go service melalui REST API. |
| Backend API         | Golang    | Menjalankan aturan bisnis, autentikasi/otorisasi request, dan akses data aplikasi.                  |
| Database            | MongoDB   | Menyimpan data yang dibutuhkan fitur Sprint 1.                                                      |

## Arsitektur Tingkat Tinggi

![Diagram arsitektur Sprint 1](./sprint-1-architecture.svg)

## Penjelasan Arsitektur

Pengunjung atau administrator membuka aplikasi Next.js yang di-deploy melalui Vercel. Aplikasi web mengirim request HTTPS ke backend Golang yang juga di-deploy melalui Vercel. Login admin memverifikasi ADMIN_ACCOUNT dan hash Argon2id pada ADMIN_CREDENTIAL, lalu membuat access/refresh session di ADMIN_SESSION. Backend menetapkan cookie HttpOnly dan memvalidasi session, status admin, role, permission, CORS origin, serta CSRF sebelum menjalankan aturan bisnis dan mengakses MongoDB. Response dikembalikan melalui jalur yang sama ke aplikasi web.

## Flowchart Login, Session, dan Proses Request

```mermaid
flowchart TD
    Start([User opens JokiHub]) --> Type{User type}

    Type -->|Visitor| PublicUI[Next.js public pages]
    Type -->|Administrator| AdminUI[Next.js admin dashboard]

    PublicUI -->|Browse games| GameRead[Go public game API]
    GameRead --> ActiveGames[(Read active games)]
    ActiveGames --> PublicUI

    AdminUI --> SessionCheck{Admin session valid?}
    SessionCheck -->|No session| LoginForm[Login form: username/email + password]
    SessionCheck -->|Access expired; refresh cookie present| RefreshAPI[Go admin refresh API]
    SessionCheck -->|Valid| AdminRequest

    LoginForm --> LoginAPI[Go admin login API]
    LoginAPI --> OriginCheck{Dashboard origin allowed?}
    OriginCheck -->|No| LoginRejected[Reject; no session created]
    OriginCheck -->|Yes| CredentialCheck{Active admin, valid hash, one active role?}
    CredentialCheck -->|No| LoginRejected
    CredentialCheck -->|Yes| CreateSession[Create ADMIN_SESSION; set access + refresh cookies; return CSRF token]
    CreateSession --> AdminRequest

    RefreshAPI --> OriginRefresh{Dashboard origin allowed?}
    OriginRefresh -->|No| SessionRequestRejected[Reject request; session unchanged]
    OriginRefresh -->|Yes| CSRFCheck{CSRF token valid?}
    CSRFCheck -->|No| SessionRequestRejected
    CSRFCheck -->|Yes| RefreshCheck{Refresh valid and unused; account ACTIVE; CSRF valid?}
    RefreshCheck -->|Yes| RotateSession[Rotate ADMIN_SESSION; set new cookies and CSRF token]
    RotateSession --> AdminRequest
    RefreshCheck -->|No| ReplayCheck{Previously rotated token reused?}
    ReplayCheck -->|Yes| RevokeFamily[Revoke session family; clear cookies]
    ReplayCheck -->|No| InvalidSession[Reject; clear cookies]
    RevokeFamily --> LoginForm
    InvalidSession --> LoginForm

    AdminRequest{Manage roles, administrator accounts, or games}
    AdminRequest --> RoleMutation{Role mutation?}
    RoleMutation -->|Yes| ProtectedCheck{Target adalah SUPER_ADMIN?}
    ProtectedCheck -->|Yes| ProtectedError[Tolak; role system immutable]
    ProtectedCheck -->|No| AdminAPI
    RoleMutation -->|No| AdminAPI
    AdminAPI --> AccessCheck{Session active, account ACTIVE, and permitted?}
    AccessCheck -->|No| AccessError[Reject; return safe error]
    AccessCheck -->|Yes| Validate{Input valid?}
    Validate -->|No| ValidationError[Reject; no protected data change]
    Validate -->|Yes| AdminData[(Read admin accounts; manage roles and games)]
    AdminData --> AdminUI
```

## Penjelasan Flow

- **Login admin:** Dashboard mengirim username/email dan password ke Go. Setelah akun ACTIVE, hash Argon2id, dan role aktif lolos pemeriksaan, backend membuat session di MongoDB lalu mengirim access/refresh cookie HttpOnly dan CSRF token.
- **Refresh session:** Dashboard mengirim refresh cookie dan CSRF token. Go merotasi refresh token satu kali; replay mencabut session family dan menghapus cookie.
- **Aksi admin:** Bootstrap menyiapkan satu role aktif `SUPER_ADMIN` yang tidak dapat diubah atau dihapus melalui API. Go memeriksa session family dan status akun pada setiap request. Akun INACTIVE atau terhapus langsung ditolak dan session family dicabut pada pemeriksaan berikutnya. Go lalu mengevaluasi role, permission, dan input sebelum membaca akun administrator atau mengelola role/game. Dashboard hanya menampilkan daftar dan detail akun admin. Permintaan tanpa akses atau input tidak valid ditolak tanpa mengubah data terlindungi.
- **Game publik:** Pengunjung meminta daftar game melalui Next.js. Go mengambil game aktif dan mengembalikan hasil untuk ditampilkan.

## SLA Baseline

Target awal untuk route dashboard, login/refresh/logout admin, dan katalog publik Sprint 1: availability bulanan 99,9%; beban NFT 50 VUser / 20 request per detik; latency baca p95 ≤ 500 ms, tulis p95 ≤ 1 detik, p99 ≤ 2 detik; kapasitas awal 2 vCPU / 4 GiB per instance dengan CPU ≤ 70% dan memori ≤ 75% saat beban target. Ini asumsi awal, bukan hasil benchmark; ukur melalui NFT sebelum rilis.

Detail kontrak mengikuti [IFA login](../solutioning/dashboard/auth/login.md), [IFA refresh token](../solutioning/dashboard/auth/refresh-token.md), [IFA logout](../solutioning/dashboard/auth/logout.md), dan Admin Account ERD untuk collection credential/session. Permission assignment custom role dan publikasi game mengikuti IFA terkait. Seed state dan sifat immutable Super Admin sudah menjadi kebijakan tetap Sprint 1.
