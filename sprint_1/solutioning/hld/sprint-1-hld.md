# Sprint 1 — High-Level Design

> **Scope:** Admin member/user management, roles, admin accounts, customer account access, and game management/discovery. Admin authentication is a prerequisite check; it does not add a new login feature.

## Flowchart

```mermaid
flowchart TD
    Start([User opens JokiHub]) --> Type{User type}

    Type -->|Visitor / Customer| PublicUI[Next.js public pages]
    Type -->|Administrator| AdminUI[Next.js admin dashboard]

    PublicUI --> CustomerAction{Choose action}
    CustomerAction -->|Register / sign in / session| AuthAPI[Go authentication API]
    AuthAPI --> AuthCheck{Valid request and credentials?}
    AuthCheck -->|No| AuthError[Return safe error]
    AuthCheck -->|Yes| CustomerSession[Create or renew customer session]
    CustomerSession --> PublicUI

    PublicUI -->|Browse games| GameRead[Go public game API]
    GameRead --> ActiveGames[(Read active games)]
    ActiveGames --> PublicUI

    AdminUI --> AdminRequest{Manage members, roles, admin accounts, or games}
    AdminRequest --> AdminAPI[Go admin API]
    AdminAPI --> AccessCheck{Authenticated and permitted?}
    AccessCheck -->|No| AccessError[Reject; return safe error]
    AccessCheck -->|Yes| Validate{Input valid?}
    Validate -->|No| ValidationError[Reject; no protected data change]
    Validate -->|Yes| AdminData[(Read or update corresponding records)]
    AdminData --> AdminUI
```

## Penjelasan flow

- **Akses customer:** Halaman Next.js mengirim registrasi, login, atau pembaruan sesi ke Go. Go memvalidasi kredensial dan mengelola sesi customer. Jika gagal, Go mengembalikan error yang aman.
- **Game publik:** Pengunjung meminta daftar game melalui Next.js. Go mengambil game aktif dan mengembalikan hasil untuk ditampilkan.
- **Aksi admin:** Dashboard mengirim permintaan ke Go. Go memeriksa sesi, permission, dan input sebelum membaca atau mengubah data member, role, akun admin, atau game. Permintaan tanpa akses atau input tidak valid ditolak tanpa mengubah data terlindungi.

Detail kebijakan identitas, session, role, dan publikasi game masih mengikuti keputusan terbuka di IFAs dan acceptance criteria terkait.
